# Google Apps Group Sync installation

## What does it do?

1. Installs Google Apps Group Sync and all dependencies to /opt/
2. Initialises the database and sets up a cron-job to run every hour

## What do you have to do?

1. Check the defaults/main configuration
2. Change the variables according to how you want them, either in defaults/main or by setting vars in the play
3. Update the initdb.json.j2 with a JSON file containing all your sync configurations
4. Add your client_secret / secret_key depending on how you want to use the app (domain wide service account or not). For instructions on how to create these see the app repo [here](https://github.com/pppontusw/gapps-ad-groups-sync)

## How can I use this role?

We can use this role to install a preconfigured version of Google Apps Group Sync to any Debian/Ubuntu/RedHat/CentOS server

For example we can pass the following settings (no service account):

```
#example-playbook.yml

- name: example
  hosts: groupsyncservers
  become: true
  vars: 
    - gapps_group_sync_user: 'root'
    - LDAPBaseDN: 'OU=OrgUnit,DC=example,DC=com'
    - LDAPUrl: 'ldap://example.com'
    - LDAPUserDN: 'gadgs@example'
    - LDAPUserPassword: 'notapassword'
    - LDAPAllUserGroupDN: 'CN=GoogleUsers,OU=OrgUnit,DC=example,DC=com'
    - CLIENT_SECRET_FILE_PATH: 'client_secret.json'
    - CLIENT_SECRET_JSON: <jsonfileetc>
  roles:
    - gapps-group-sync
```

and a JSON file that looks like this:

```
#initdb.json.j2

[{
    "gappslist": "nordics@example.com",
    "members": [
        {
        "attribute": "physicalDeliveryOfficeName",
        "value": "Denmark"
        },
        {
        "attribute": "physicalDeliveryOfficeName",
        "value": "Sweden"
        },
        {
        "attribute": "physicalDeliveryOfficeName",
        "value": "Norway"
        },
        {
        "attribute": "physicalDeliveryOfficeName",
        "value": "Finland"
        }
    ],
    "type": "AND"
    },
    {
    "gappslist": "US-IT@example.com",
    "members": [
        {
        "attribute": "physicalDeliveryOfficeName",
        "value": "US"
        },
        {
        "attribute": "Department",
        "value": "IT"
        }
    ],
    "type": "OR"
}]
```

``` ansible-playbook example-playbook.yml ```

## Compability

Tested on:

Debian 8 & CentOS 7
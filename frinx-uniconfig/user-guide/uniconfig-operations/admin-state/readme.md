# Admin State

Admin state is used to lock, unlock or southbound-lock devices. 
Modification of data on those devices is then allowed or forbidden 
accordingly. Currently, there are three states that are supported:

* LOCKED - When a device is administratively locked, it is not possible 
  to modify its configuration, and no changes are ever pushed to the device.
* UNLOCKED - Device is assumed to be operational. All changes are attempted
  to be sent southbound. This is the default when a new device is created.
* SOUTHBOUND_LOCKED - It is possible to configure the device, but no changes 
  are sent to the device. Admin mode is useful when pre provisioning devices.

This state is automatically added to the device during installation. The user
can further specify what state the device should be in, via:
    
**"uniconfig-config:admin-state": "unlocked"**

The state variable should be from one of the above-mentioned options.

If the user wants to change the state after the installation, an RPC for
changing that state is available.

## RPC Example

RPC input contains the device name and the state that it should be change to.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/connection-manager:change-admin-state' \
--header 'Authorization: Basic YWRtaW46YWRtaW4=' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input": {
        "node-id": "vnf20",
        "admin-state": "southbound_locked"
    }
}'
```
```json RPC Response, Status: 200
{
    "output": {
        "status": "complete"
    }
}
```

## RPC Example

GET request to get the actual state of the device.

```bash RPC Request
curl --location --request GET 'http://localhost:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=vnf20/uniconfig-config:admin-state' \
--header 'Authorization: Basic YWRtaW46e3twYXNzd29yZH19'
```
```json RPC Response, Status: 200
{
    "uniconfig-config:admin-state": "unlocked"
}
```

# RPC Failed Example

Device is in **locked** admin-state and the user tries to modify data on the device.

```bash RPC Request
curl --location --request PUT 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node={{node_id}}/configuration/confdConfig/ssh/clientAliveCountMax' \
--header 'Authorization: Basic YWRtaW46YWRtaW4=' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data-raw '{
    "clientAliveCountMax": 4
}'
```
```json RPC Response, Status: 400
{
    "errors": {
        "error": [
            {
                "error-message": "4af87c96-e49b-4e62-9217-316fe966d3d1: The commit RPC returned FAIL status. \n The node: '{{node_id}}' is currently in admin-state LOCKED.",
                "error-tag": "bad-element",
                "error-type": "rpc"
            }
        ]
    }
}
```
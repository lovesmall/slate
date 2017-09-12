# Drone

## Get Drones

### HTTP Request

`GET http://tango.avetics.com/api/v1/drones`

> JSON response:

```json
{
  "data": [
    {
      "id": "3",
      "type": "drones",
      "attributes": {
        "name": "Drone 101",
        "status": "Ok",
        "devise-sn": ""
      }
    },
    {
      "id": "5",
      "type": "drones",
      "attributes": {
        "name": "Drone 102",
        "status": "Ok",
        "devise-sn": "123DEF"
      }
    }
  ]
}
```


## Set Drone Devise SN

### HTTP Request

`PUT http://tango.avetics.com/api/v1/drones/:id`

> JSON response:

```json
{
    "data": {
        "id": "3",
        "type": "drones",
        "attributes": {
            "name": "Drone 101",
            "status": "ok",
            "devise-sn": "123ABC"
        }
    }
}
```

### URL Parameters

| Parameter | Description                      |
| --------- | -------------------------------- |
| ID        | The ID of the drone to update    |

### PUT Form Parameters

| Parameter | Description                      |
| --------- | -------------------------------- |
| drone[devise_sn] | The devise sn to set    |


<aside class="info">
You can set drone devise sn only if it's empty.
</aside>
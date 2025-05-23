# easyTradeThirdPartyService

A java service that should not be monitored and handles credit card manufacture and delivery.

## Technologies used

- Java 21
- Docker

## Local build instructions

```bash
docker build -t easytradethirdpartyservice .
docker run -d -p 8093:8080 --name third-party-service -e CREDIT_CARD_ORDER_SERVICE_HOSTANDPORT=credit-card-order-service:8080 -e COURIER_DELAY=400 -e COURIER_RATE=900 -e MANUFACTURE_DELAY=500 -e MANUFACTURE_RATE=900 -e PROXY_PREFIX=third-party-service -e FEATURE_FLAG_SERVICE_PROTOCOL=http -e FEATURE_FLAG_SERVICE_BASE_URL=feature-flag-service -e FEATURE_FLAG_SERVICE_PORT=8080
```

## Endpoints or logic

### Swagger

---

Swagger endpoint is available at:

```bash
# when deployed with k8s
http://SOMEWHERE/third-party-service/swagger-ui/index.html
```

### Problem pattern

---

Third party service has support for one problem pattern - `factory_crisis`. When this problem pattern is enabled, no new credit cards will be created by manufacturer as long as the problem pattern is ON. Problem pattern can be enabled using the api provided with the feature flag service. More information on using the feature flag service is available in the [feature flag service readme](./feature-flag-service.md).

### Api v1

---

#### `POST` **/v1/manufacturer** `Produce a new credit card`

##### Parameters

| name                | type     | data type | description                            | source    |
| ------------------- | -------- | --------- | -------------------------------------- | --------- |
| `creditCardOrderId` | required | string    | Credit card order ID                   | Body JSON |
| `name`              | required | string    | Name of card holder                    | Body JSON |
| `cardLevel`         | required | string    | Type of card: silver, gold or platinum | Body JSON |

##### Responses

| http code | content-type       | response                                                              |
| --------- | ------------------ | --------------------------------------------------------------------- |
| `200`     | `application/json` | `{"statusCode": 200, "message": "Credit card is being manufactured"}` |

##### Example of request JSON body

```json
{
  "creditCardOrderId": "b0404285-41ca-4748-a8d9-8a104a0a9d08",
  "name": "John Doe",
  "cardLevel": "silver"
}
```

##### Example cURL

```bash
curl -X 'POST' \
  'http://localhost/third-party-service/v1/manufacturer' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "creditCardOrderId": "b0404285-41ca-4748-a8d9-8a104a0a9d08",
  "name": "John Doe",
  "cardLevel": "silver"
}'
```

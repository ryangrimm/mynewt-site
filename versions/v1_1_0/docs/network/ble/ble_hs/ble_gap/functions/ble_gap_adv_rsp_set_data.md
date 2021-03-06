## <font color="#F2853F" style="font-size:24pt">ble\_gap\_adv\_rsp\_set\_data</font>

```c
int
ble_gap_adv_rsp_set_data(
    const uint8_t *data,
              int  data_len
)
```

### Description

Configures the data to include in subsequent scan responses.

### Parameters

| *Parameter* | *Description* |
|-------------|---------------|
| data | Buffer containing the scan response data. |
| data\_len | The size of the response data, in bytes. |

### Returned values

| *Value* | *Condition* |
|---------|-------------|
| 0 | Success. |
| BLE\_HS\_EBUSY | Advertising is in progress. |
| [Core return code](../../ble_hs_return_codes/#return-codes-core) | Unexpected error. |

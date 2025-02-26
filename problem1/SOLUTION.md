Provide your CLI command here:
- Using `jq`
```sh
curl -s https://example.com/api/:order_id | jq 'select(.symbol == "TSLA")' > output.txt
```
- Using `grep`
```sh
curl -s https://example.com/api/:order_id | grep '"symbol": "TSLA"' > output.txt
```

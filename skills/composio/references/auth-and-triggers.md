# Composio Auth and Triggers Reference

Authentication blueprints and event triggers for connected toolkits.

## Connected Accounts & OAuth Flows
1. Initiate OAuth connection:
```python
connection_request = entity.initiate_connection(app_name="github")
redirect_url = connection_request.redirect_url
# Direct user to redirect_url to authenticate
```
2. Check connection status:
```python
is_connected = entity.is_app_authenticated(app_name="github")
```

## Trigger Listeners
Subscribe to external events:
```python
# Listen to new GitHub commits or issue creations
listener = client.create_trigger_listener()

@listener.on("GITHUB_COMMIT_EVENT")
def handle_commit(payload):
    print("Received commit:", payload.data)

listener.start()
```

## Webhook Validation
- Verify trigger HMAC signatures before processing webhooks.
- Ensure callback URLs match the registered HTTPS origin.

---
sidebar_label: Working with Silent Renew
sidebar_position: 9
---

# Working with Silent Renew

The tokens can be renewed in two ways:

1. Using silent renew with refresh tokens or
2. Using silent renew with iframes.

Both of these approaches are not perfect and bring some disadvantages. The security best practices for the renew process should be applied as much as possible. See the latest OAuth specs, drafts and recommendations for further information.

## Silent Renew Code Flow with PKCE using refresh tokens

For session renewal with the PKCE flow, no iframes are used by default. Instead, a refresh token is used to obtain a new access token from the auth server. Thus, the renewal only needs to be configured in the app module. Some servers require the `offline_access` scope for this to work which is defined in the OIDC specifications. Other servers do not require this.

Refresh tokens should be rotated and the refresh token should be revoked on a logout using the revocation endpoint.

## Silent Renew (iframe)

When silent renew is enabled, a DOM event will automatically be installed in the application's host window. The event `oidc-silent-renew-message` accepts a `CustomEvent` instance with the token returned from the OAuth server in its `detail` field. The event handler will send this token to the authorization callback and complete the validation.

Point the `silent_renew_url` property to an HTML file which contains the following script element to enable authorization.

The `renewTimeBeforeTokenExpiresInSeconds` property can be used to start the renew process n-seconds before the tokens expired.

Both the access token and the id_token are used to start this process.

### Silent Renew Code Flow with PKCE (iframe)

```javascript
<script>
window.onload = function () {
  /* The parent window hosts the Angular application */
  const parent = window.parent;
  /* Send the id_token information to the oidc message handler */
  const event = new CustomEvent('oidc-silent-renew-message', { detail: window.location });
  parent.dispatchEvent(event);
};
</script>
```

If you are working with the [Angular CLI](https://angular.io/cli) make sure you add the `silent-renew.html` file to the assets configuration in your `angular.json`. This has already been done for you if you used the `ng add` schematics to install and setup the library.

```json
"assets": [
  "projects/<your-project-here>/src/silent-renew.html",
],
```

### Silent Renew Implicit Flow (iframe)

```javascript
<script>
window.onload = function () {
  /* The parent window hosts the Angular application */
  const parent = window.parent;
  /* Send the id_token information to the oidc message handler */
  const event = new CustomEvent('oidc-silent-renew-message', { detail: window.location.hash.substr(1) });
  parent.dispatchEvent(event);
};
</script>
```

## Security Token Service CSP and CORS

When silent renew is enabled, the lib will attempt to perform a renew before returning the authorization state.
This allows the application to authorize a user, that is already authenticated, without performing redirects.

Silent renew requires CSP configuration on the server to allow iframes and also CORS.

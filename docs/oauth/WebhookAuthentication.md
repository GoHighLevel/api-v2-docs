---
stoplight-id: vyc3gbbez52ip
---

# Webhook Authentication Guide

## How It Works

### 1. Receiving the Webhook

When your endpoint receives a webhook request, it will include the following:

- **Headers**:

  - `x-wh-signature`: The digital signature of the payload.

- **Body**: The payload containing the timestamp, webhook ID, and data.

Example payload:

    {
      "timestamp": "2025-01-28T14:35:00Z",
      "webhookId": "abc123xyz",
      ...<add_other_webhook_data>
    }


### 2. Verifying the Signature

To verify the authenticity of the webhook request:

1. Retrieve the `x-wh-signature` header from the request.

2. Use the public key mentioned below to verify the signature:

3. Compute the signature on your end using the payload and the public key.

4. Compare your computed signature with the `x-wh-signature` header.

```
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAokvo/r9tVgcfZ5DysOSC
Frm602qYV0MaAiNnX9O8KxMbiyRKWeL9JpCpVpt4XHIcBOK4u3cLSqJGOLaPuXw6
dO0t6Q/ZVdAV5Phz+ZtzPL16iCGeK9po6D6JHBpbi989mmzMryUnQJezlYJ3DVfB
csedpinheNnyYeFXolrJvcsjDtfAeRx5ByHQmTnSdFUzuAnC9/GepgLT9SM4nCpv
uxmZMxrJt5Rw+VUaQ9B8JSvbMPpez4peKaJPZHBbU3OdeCVx5klVXXZQGNHOs8gF
3kvoV5rTnXV0IknLBXlcKKAQLZcY/Q9rG6Ifi9c+5vqlvHPCUJFT5XUGG5RKgOKU
J062fRtN+rLYZUV+BjafxQauvC8wSWeYja63VSUruvmNj8xkx2zE/Juc+yjLjTXp
IocmaiFeAO6fUtNjDeFVkhf5LNb59vECyrHD2SQIrhgXpO4Q3dVNA5rw576PwTzN
h/AMfHKIjE4xQA1SZuYJmNnmVZLIZBlQAF9Ntd03rfadZ+yDiOXCCs9FkHibELhC
HULgCsnuDJHcrGNd5/Ddm5hxGQ0ASitgHeMZ0kcIOwKDOzOU53lDza6/Y09T7sYJ
PQe7z0cvj7aE4B+Ax1ZoZGPzpJlZtGXCsu9aTEGEnKzmsFqwcSsnw3JB31IGKAyk
T1hhTiaCeIY/OwwwNUY2yvcCAwEAAQ==
-----END PUBLIC KEY-----
```

If they match, the payload is valid and comes from a trusted source.

### 3. Handling Replay Attacks

To protect against replay attacks:

- Ensure the `timestamp` in the payload is within an acceptable time window (e.g., 5 minutes).

- Reject any requests with duplicate `webhookId` values.

### 4. Handling Public Key Rotation

Please keep an eye on your email and [our social channels](https://ghl-developer-council.slack.com/archives/C01F43GUJV6) for notices regarding public key rotation to stay updated. The public key in this doc is the one to use to validate the webhook payload.

***


## Example Code

Here’s an example of how to verify the signature in Node.js:

    const crypto = require('crypto');

    const publicKey = `<use_the_above_key>`;

    function verifySignature(payload, signature) {
        const verifier = crypto.createVerify('SHA256');
        verifier.update(payload);
        verifier.end();

        return verifier.verify(publicKey, signature, 'base64');
    }

    // Example usage
    const payload = JSON.stringify({
      "timestamp": "2025-01-28T14:35:00Z",
      "webhookId": "abc123xyz",
      ...<add_other_webhook_data>
    });

    const signature = "<received-x-wh-signature>";
    const isValid = verifySignature(payload, signature);

    return isValid;


## Summary

These new features significantly enhance the security of webhook integrations. By including a timestamp, webhook ID, and a digitally signed payload, we ensure your data remains secure and trusted. Implement these changes today to keep your integrations robust and secure!

# 'invalid_grant: Bad Request' error

I was recently having issues accessing an API in Google Cloud. (Specifically, it was the BigQuery Data Transfer API, but this error would impact all of them.)

I had the necessary permissions to complete the tasks I was attempting to do, but all of my requests were failing with a 503 error and the following:

```bash
('invalid_grant: Bad Request', {'error': 'invalid_grant', 'error_description': 'Bad Request'})
```

In the console, I checked the application default credentials:

```bash
$ gcloud auth application-default print-access-token
ERROR: (gcloud.auth.application-default.print-access-token) There was a problem refreshing your current auth tokens: ('invalid_grant: Bad Request', {'error': 'invalid_grant', 'error_description': 'Bad Request'})
Please run:

  $ gcloud auth application-default login

to obtain new credentials.
```

To fix I ran `gcloud auth application-default login` as suggested.  This took me to the browser to authenticate, confirm, etc.

When digging around, I found the following post helpful: [Google OAuth "invalid_grant" nightmare](https://blog.timekit.io/google-oauth-invalid-grant-nightmare-and-how-to-fix-it-9f4efaf1da35)

It turns out that I had recently changed my password, which happens to be one of the items in his list.

## References

- [gcloud auth](https://cloud.google.com/sdk/gcloud/reference/auth)

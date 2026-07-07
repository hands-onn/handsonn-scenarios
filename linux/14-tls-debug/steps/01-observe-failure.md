# Observe the failure

nginx is running locally on `8443` with a self-signed cert. Try to talk
to it without shortcuts:

```bash
curl -v https://127.0.0.1:8443/
```

You'll get an error like:

```
* SSL certificate problem: self-signed certificate
* SSL certificate problem: unable to get local issuer certificate
```

or (if the cert would validate but the hostname is wrong):

```
* SSL: no alternative certificate subject name matches target host name
```

curl's exit code is non-zero. Save it:

```bash
curl -sS https://127.0.0.1:8443/ >/dev/null 2>&1; echo $? > /tmp/curl-exit
cat /tmp/curl-exit
```

**Never** just add `-k` in production code to make this go away. You'd
be turning off exactly the check that protects the connection.

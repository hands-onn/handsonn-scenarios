The check greps for `wrong-name.example` in /tmp/cn. Pipe `openssl x509 -noout -subject` output through sed to strip the leading `subject=CN =` prefix so only the value lands in the file.

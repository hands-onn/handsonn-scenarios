The Ingress `ADDRESS` is empty because no **ingress controller** is running.
The object is only declarative data — a controller must watch it and program
a real proxy/LB. Write that reason to `/tmp/why-no-traffic`; the check greps
for words like "controller" / "no ingress" / "not installed".

`kubectl create role secret-reader --verb=get --verb=list --resource=secrets`. The check reads the Role's first rule and expects `secrets` in resources.

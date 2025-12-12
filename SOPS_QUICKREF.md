# SOPS Quick Reference

## Encrypt Secrets
```bash
# Encrypt existing file
sops --age age1qnk73unz2j5puajensuw3pj48x5yfmml65s2czecwqu8a4tez9msz70erc -e -i secret.yaml

# Create new encrypted secret
echo 'apiVersion: v1
kind: Secret
metadata:
  name: my-secret
stringData:
  password: mypassword' > secret.yaml

sops --age age1qnk73unz2j5puajensuw3pj48x5yfmml65s2czecwqu8a4tez9msz70erc -e -i secret.yaml
mv secret.yaml secret.enc.yaml
```

## Decrypt Secrets
```bash
# Decrypt to stdout
sops -d secret.enc.yaml

# Decrypt to file
sops -d secret.enc.yaml > secret.yaml
```

## Auto-encrypted Files
- `*.enc.yaml`
- `secrets/*.yaml`
- `*.sops.yaml`

## Age Key
Public: `age1qnk73unz2j5puajensuw3pj48x5yfmml65s2czecwqu8a4tez9msz70erc`
Private: `age.agekey` (never commit this!)
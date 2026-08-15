# SMBIOS setup

The checked-in `config.plist` deliberately contains invalid placeholder identity values.

For this EFI, the selected model is:

```text
MacBookPro16,1
```

Generate a fresh identity for your own machine and replace:

- `SystemSerialNumber`
- `MLB`
- `SystemUUID`
- `ROM`

Never publish or copy a real machine's Serial/MLB/UUID/ROM into a public EFI repository.

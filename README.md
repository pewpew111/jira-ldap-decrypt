# jira-ldap-decrypt
Decrypt jira ldap password

Get password from DB 
```bash
select attribute_value from cwd_directory_attribute where attribute_name = 'ldap.password';
```
result:
```bash
{AES_CBC_PKCS5Padding}{"keyFilePath":"KEY_DIR/javax.crypto.spec.SecretKeySpec_1232132121771","serializedSealedObject":"rO0ABXNyABlqYXZh....1BLQ1M1UGFkZGluZw\u003d\u003d"}
```
Then convert \u unicode entities to "=" - can be done in CyberChef, and create sealedObjectFile
```bash
echo -n "rO0ABXNyABlqYXZh....1BLQ1M1UGFkZGluZw==" | base64 -d > /tmp/sealedObject.file
```
File **javax.crypto.spec.SecretKeySpec_1232132121771** should be in */var/atlassian/application-data/jira/keys/*
```bash
#go to jira bin folder
cd /opt/atlassian/jira/bin/

#run decryption jar
java -cp "./*" com.atlassian.db.config.password.tools.CipherTool -c com.atlassian.db.config.password.ciphers.algorithm.AlgorithmCipher -m decrypt

#insert this as password
{"algorithm":"AES/CBC/PKCS5PADDING","keyFilePath":"/var/atlassian/application-data/jira/keys/javax.crypto.spec.SecretKeySpec_1232132121771","sealedObjectFilePath":"/tmp/sealedObject.file"}

```
You will receive somethin like this
```bash
superpass|SALT-12323a1-1250-43b0-adf8-f0123f99b9f9
```


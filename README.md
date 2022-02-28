set +x;
ENV=$1
SECRETS_PROXY_TRUSTSTORE="secrets_proxy_truststore.p12"
PORT=443

cp -rf pciqa.application.conf src/main/resources/application.conf
SECRETS_PROXY_DOMAIN="proxy.$ENV.com"
SECRETS_PROXY_BASEURL="proxy.$ENV.com"



echo "Calling $ENV - $SECRETS_PROXY_DOMAIN:$PORT"

echo -n | openssl s_client -showcerts -connect $SECRETS_PROXY_DOMAIN:$PORT | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > globalsign.crt 
echo 'yes' | keytool -importcert -trustcacerts -alias globalsign-rootca -storetype PKCS12 -keystore $SECRETS_PROXY_TRUSTSTORE -storepass changeit -file globalsign.crt
mv $SECRETS_PROXY_TRUSTSTORE src/main/resources/keystores/
rm -f globalsign.crt

echo "************** Trusstore generation complete!!! *********************"

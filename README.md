set +x;
ENV=$1
SECRETS_PROXY_TRUSTSTORE="truststore.p12"
PORT=443

cp -rf application.conf src/main/resources/application.conf
DOMAIN="proxy.$ENV.com"
BASEURL="proxy.$ENV.com"



echo "Calling $ENV - $DOMAIN:$PORT"

echo -n | openssl s_client -showcerts -connect $DOMAIN:$PORT | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > globalsign.crt 
echo 'yes' | keytool -importcert -trustcacerts -alias globalsign-rootca -storetype PKCS12 -keystore $TRUSTSTORE -storepass changeit -file globalsign.crt
mv $TRUSTSTORE src/main/resources/keystores/
rm -f globalsign.crt

echo "************** Trusstore generation complete!!! *********************"

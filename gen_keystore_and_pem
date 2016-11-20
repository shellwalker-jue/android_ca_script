#!/bin/bash

OUT_DIR=out
BASIC_KEY_STORE=$OUT_DIR/all.keystore
ROOT_CA_ALIAS=TestRootCA
ROOT_PEM=$OUT_DIR/ca.pem

TARGET_ALIAS=mockserver
TARGET_KEYSTORE=$OUT_DIR/$TARGET_ALIAS.keystore
TARGET_CSR=$OUT_DIR/$TARGET_ALIAS.csr
TARGET_CRT=$OUT_DIR/$TARGET_ALIAS.crt
TARGET_PEM=$OUT_DIR/$TARGET_ALIAS.pem

DEFAULT_PASS=123456
BC_EXT_PARAS="-provider org.bouncycastle.jce.provider.BouncyCastleProvider -storetype BKS -providerpath bcprov-jdk15on-155.jar"


rm -rf $OUT_DIR
mkdir $OUT_DIR

echo "Gen CA key"
keytool -genkeypair -alias $ROOT_CA_ALIAS -keyalg RSA -keysize 2048 -validity 3650 -keypass $DEFAULT_PASS -storepass $DEFAULT_PASS -dname "CN=Test Root CA, OU=Test Root CA, O=Engineering Tool, L=WUHAN, ST=HUBEI, C=CN" -keystore $BASIC_KEY_STORE

echo "Gen $TARGET_ALIAS key"
keytool -genkeypair -alias $TARGET_ALIAS -keyalg RSA -keysize 2048 -validity 3650 -keypass $DEFAULT_PASS -storepass $DEFAULT_PASS -dname "CN=127.0.0.1, OU=$TARGET_ALIAS, O=2012 Lab, L=SHENZHEN, ST=SHENZHEN, C=CN" -keystore $BASIC_KEY_STORE

echo "Gen $TARGET_ALIAS certreq"
keytool -certreq -alias $TARGET_ALIAS -file $TARGET_CSR -storepass $DEFAULT_PASS -keystore $BASIC_KEY_STORE

echo "Gen $TARGET_ALIAS cert signed by CA"
keytool -gencert -alias $ROOT_CA_ALIAS -infile $TARGET_CSR -outfile $TARGET_CRT -storepass $DEFAULT_PASS -keystore $BASIC_KEY_STORE

echo "Import $TARGET_ALIAS cert"
keytool -importcert -alias $TARGET_ALIAS -file $TARGET_CRT -storepass $DEFAULT_PASS -keystore $BASIC_KEY_STORE

echo "Copy $TARGET_ALIAS keystore"
cp $BASIC_KEY_STORE $TARGET_KEYSTORE
keytool -delete -alias $ROOT_CA_ALIAS -storepass $DEFAULT_PASS -keystore $TARGET_KEYSTORE

echo "Export pem from $ROOT_CA_ALIAS & $TARGET_ALIAS"
keytool -exportcert -alias $TARGET_ALIAS -file $TARGET_PEM -storepass $DEFAULT_PASS -rfc -keystore $TARGET_KEYSTORE
keytool -exportcert -alias $ROOT_CA_ALIAS -file $ROOT_PEM -storepass $DEFAULT_PASS -rfc -keystore $BASIC_KEY_STORE

echo "Convert $TARGET_ALIAS keystore from JKS to BKS"
keytool -importkeystore -srckeystore $TARGET_KEYSTORE -srcstoretype JKS -srcstorepass $DEFAULT_PASS -destkeystore $TARGET_KEYSTORE.bks -deststoretype BKS -deststorepass $DEFAULT_PASS $BC_EXT_PARAS



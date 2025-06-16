echo “Data data data” > secret.txt

aws configure

aws kms encrypt --key-id <replace-key-id> --plaintext fileb://secret.txt --output text --query CiphertextBlob | base64 --decode > encryptedsecret.txt
cat encryptedsecret.txt
aws kms decrypt --ciphertext-blob fileb://encryptedsecret.txt --output text --query Plaintext | base64 --decode > decryptedsecret.txt
cat decryptedsecret.txt



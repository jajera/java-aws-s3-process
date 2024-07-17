# java-aws-s3-process

export AWS_VAULT_FILE_PASSPHRASE="$(cat /root/.awsvaultk)"

aws-vault exec dev -- terraform -chdir=./terraform/01 init
aws-vault exec dev -- terraform -chdir=./terraform/01 apply --auto-approve

source ./terraform/01/terraform.tmp

## java

mkdir java

cd java

mvn archetype:generate \
  -DarchetypeGroupId=software.amazon.awssdk \
  -DarchetypeArtifactId=archetype-app-quickstart \
  -DarchetypeVersion=2.20.43 \
  -Dservice=s3 \
  -DhttpClient=apache-client \
  -DnativeImage=false \
  -DcredentialProvider=identity-center \
  -DgroupId=org.example \
  -DartifactId=s3 \
  -Dversion=1.0-SNAPSHOT \
  -Dpackage=org.example \
  --batch-mode

mvn -f ./s3/pom.xml clean package

mvn -f ./s3/pom.xml exec:java -Dexec.mainClass="org.example.App"

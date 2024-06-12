README.md


CANAME=my-org

# optional
mkdir $CANAME
cd $CANAME

# generate aes encrypted private key
openssl genrsa -out $CANAME.key 4096

# create certificate, 1826 days = 5 years
# the following will ask for common name, country, ...
openssl req -x509 -new -nodes -key $CANAME.key -sha256 -days 1826 -out $CANAME.crt

# ... or you provide common name, country etc. via:
openssl req -x509 -new -nodes -key $CANAME.key -sha256 -days 1826 -out $CANAME.crt -subj '/CN=MyOrg Root CA/C=DE/ST=Heilbronn/L=BW/O=MyOrg'




curl -s https://fluxcd.io/install.sh | sudo bash

flux bootstrap github \
  --token-auth \
  --owner=Daudkhan1 \
  --repository=flux-get-started \
  --branch=main \
  --path=./clusters/dml \
  --personal



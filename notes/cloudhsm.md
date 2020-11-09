# trials in getting microsoft signing tool and cloudHSM working from a docker container

## starting steps

- follow the starting out steps for AWS CloudHSM so you have that customerCA.crt that'll let you talk to your HSM from places, currently at <https://docs.aws.amazon.com/cloudhsm/latest/userguide/getting-started.html>
- setup a crypto_user for your windows server/container/box to use in the next step so it can talk to the Cavium Key Storage Provider (CSP)
- also follow the KSP/CNG steps under using the software libraries, currently at <https://docs.aws.amazon.com/cloudhsm/latest/userguide/ksp-library.html>, initially you'll want to run set_cloudhsm_credentials, so the windows EC2 instance can talk to the CSP/CloudHSM.
- associate your installed cert with the private key thats in the HSM, steps currently listed <https://docs.aws.amazon.com/cloudhsm/latest/userguide/ksp-library-associate-key-certificate.html>
  - import cert, i.e. certreq -accept certificatename
  - Associate the AWS CloudHSM private key with the certificate, i.e. import_key.exe –RSA CertReq-xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx
  - update the certificate store, i.e. certutil -f -csp "Cavium Key Storage Provider"-repairstore my "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx"
- sign things
 
## appendix

NTE_NOT_SUPPORTED - our initial request.inf had Exportable = True, so we tried ExportableEncrypted = True, understandably CSP doesn't like that, so once removed the csr was created.

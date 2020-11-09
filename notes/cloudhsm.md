# trials in getting microsoft signing tool and cloudHSM working from a docker container

## starting steps

- follow the starting out steps for AWS CloudHSM so you have that customerCA.crt that'll let you talk to your HSM from places, currently at <https://docs.aws.amazon.com/cloudhsm/latest/userguide/getting-started.html>
- also follow the KSP/CNG steps under using the software libraries, currently at <https://docs.aws.amazon.com/cloudhsm/latest/userguide/ksp-library.html>, initially you'll want to run set_cloudhsm_credentials, so the windows EC2 instance can talk to the CloudHSM... Cavium Key Storage Provider.
 

# trials in getting microsoft signing tool and cloudHSM working from a docker container

## starting steps

follow the starting out steps for AWS CloudHSM so you have that customerCA.crt that'll let you talk to your HSM from places.
include the bonus step of running set_cloudhsm_credentials [currently hosted at https://docs.aws.amazon.com/cloudhsm/latest/userguide/ksp-library-prereq.html],
 so the windows EC2 instance will talk to the CloudHSM... Cavium Key Storage Provider.
 

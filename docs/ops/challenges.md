# Challenges
This projekt was originally plant to run on kubernetes. Due to limited server-resources we changed course to use docker-compose instead. Kubernetes was more likely to consume storage by writing to logs which aren't directly associated with kubernetes and are not removed if kubernetes-data is cleared. This logging-issue was first discovered after the switch to a docker-compose based configuration. Due to the late integration-phase of this projekt, there wasn't enough time to react adequatly to this requirements-change which led to a time-consuming administration job because instances of services need to be first stopped, their images deleted and then new pulled instead of just pulling and replacing a running service. Furthermore logs needed to be limited to a small size which made debugging a interactive job because missed error-logs were overwriten not even a minute later.
# Deploy Kserve Prow Bot on IBM Cloud

The Kserve Prow Bot deployment is a modified version based on the [Prow s3 starter](https://github.com/kubernetes/test-infra/blob/master/config/prow/cluster/starter-s3.yaml) for IBM Cloud. Please follow the below instrctions to deploy/update the Kserve Bot.

## GitHub bot account

Before using `tackle` or deploying prow manually, ensure you have created a
GitHub account for prow to use.  Prow will ignore most GitHub events generated
by this account, so it is important this account be separate from any users or
automation you wish to interact with prow. For example, you still need to do
this even if you'd just setting up a prow instance to work against your own
personal repos.

1. Ensure the bot user has the following permissions
    - Write access to the repos you plan on handling
    - Owner access (and org membership) for the orgs you plan on handling (note
      it is possible to handle specific repos in an org without this)
1. Create a [personal access token][1] for the GitHub bot account, adding the
   following scopes (more details [here][8])
    - Must have the `public_repo` and `repo:status` scopes
    - Add the `repo` scope if you plan on handing private repos
    - Add the `admin:org_hook` scope if you plan on handling a github org

# Kserve Prow Bot Deployment

1. Update the credentials under the `prow-parameters` section in [kustomize/kustomization.yaml](kustomize/kustomization.yaml). The Bot credentials are located within the internal team's Kserve folder.

2. Once the credentials are updated, modify any configuration within [kustomize/prow-ibm-cloud.yaml](kustomize/prow-ibm-cloud.yaml) if necessary. Then, run the following commands to deploy the Kserve Prow Bot on an IBM Cloud Kubernetes Cluster.
```shell
kubectl apply -k kustomize
```

3. As the Kserve admin, please update the [Kserve Github Webhook](https://github.com/organizations/kserve/settings/hooks) to the following
```
Payload URL: https://$(prow-ingress)/hook
Content type: application/json

Secret: $(hmac-token) in manifests

SSL verification: Enable
Which events would you like to trigger this webhook?: Send me everything.
```

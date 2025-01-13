# External Secrets Operator

This project was born as a kind of PoC of adding easily the needed operator-sdk envelope to current [helm chart external-secrets operator](https://github.com/external-secrets/external-secrets) without doing any change on the existing helm chart operator, so the operator could be installed natively through OLM (Operator Lifecyle Manager).

This PoC project was donated to the [external-secrets community](https://github.com/external-secrets) in upstream issue https://github.com/external-secrets/external-secrets/issues/493, so ESO maintainers were able to do releases of both helm charts and OLM operator at [OperatorHub.io](https://operatorhub.io).

The donation was done via fork, and it is actively maintained at https://github.com/external-secrets/external-secrets-helm-operator

## Purpose of the External Secrets Operator

The External Secrets Operator (ESO) is designed to synchronize secrets from external secret management systems (like AWS Secrets Manager, Azure Key Vault, Google Secret Manager, etc.) into Kubernetes. This allows you to manage your secrets in a centralized and secure manner, while still making them available to your applications running in Kubernetes.

## Installation using OLM

To install the External Secrets Operator using the Operator Lifecycle Manager (OLM), follow these steps:

1. Ensure that you have OLM installed in your Kubernetes cluster. You can find the installation instructions [here](https://github.com/operator-framework/operator-lifecycle-manager#installation).

2. Install the External Secrets Operator by running the following command:

   ```sh
   kubectl create -f https://operatorhub.io/install/external-secrets-operator.yaml
   ```

3. Verify that the operator is running:

   ```sh
   kubectl get pods -n operators
   ```

   You should see a pod with a name that starts with `external-secrets-operator`.

## Using the External Secrets Operator

To use the External Secrets Operator, follow these steps:

1. Create a Kubernetes secret that contains the credentials for your external secret management system. For example, if you are using AWS Secrets Manager, you can create a secret with your AWS access key and secret access key:

   ```sh
   kubectl create secret generic aws-credentials --from-literal=aws_access_key_id=<your-access-key-id> --from-literal=aws_secret_access_key=<your-secret-access-key>
   ```

2. Create an `ExternalSecret` resource that specifies the external secret you want to synchronize and the target Kubernetes secret:

   ```yaml
   apiVersion: external-secrets.io/v1alpha1
   kind: ExternalSecret
   metadata:
     name: my-secret
   spec:
     backendType: secretsManager
     data:
       - key: my-secret-key
         name: my-secret-value
     secretStoreRef:
       name: aws-credentials
   ```

3. Apply the `ExternalSecret` resource to your cluster:

   ```sh
   kubectl apply -f my-secret.yaml
   ```

4. Verify that the secret has been synchronized:

   ```sh
   kubectl get secret my-secret
   ```

   You should see a secret with the name `my-secret` in your cluster.

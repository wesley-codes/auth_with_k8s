# Auth with Kubernetes

This project demonstrates how to set up authentication and authorization in Kubernetes using a Certificate Signing Request (CSR) and Role-Based Access Control (RBAC).

## Project Structure

auth_with_k8s/ ├── .gitignore # Specifies files to be ignored by Git ├── bob.crt # Certificate for the user 'bob' ├── bob.csr # Certificate Signing Request for 'bob' ├── bob.key # Private key for the user 'bob' ├── role.yaml # RBAC Role definition ├── rolebinding.yaml # RBAC RoleBinding definition ├── signin-request.yaml # CertificateSigningRequest for 'bob'

## Files Overview

- **`bob.key`**: The private key for the user `bob`. This file is sensitive and should not be shared.
- **`bob.csr`**: The Certificate Signing Request for `bob`, used to request a certificate from the Kubernetes API server.
- **`bob.crt`**: The signed certificate for `bob`, issued by the Kubernetes API server.
- **`role.yaml`**: Defines a Role named `pod-reader` that grants read-only access to Pods in the `k8auth` namespace.
- **`rolebinding.yaml`**: Binds the `pod-reader` Role to the user `bob` in the `k8auth` namespace.
- **`signin-request.yaml`**: A Kubernetes CertificateSigningRequest resource for `bob` to request a client certificate.

## Usage

1. **Generate a Private Key and CSR**:
   - The private key (`bob.key`) and CSR (`bob.csr`) are already included in this project. If you need to regenerate them, use the following command:
     ```bash
     openssl req -new -newkey rsa:2048 -nodes -keyout bob.key -out bob.csr -subj "/CN=bob/O=leaner"
     ```

2. **Submit the CSR to Kubernetes**:
   - Apply the `signin-request.yaml` file to submit the CSR:
     ```bash
     kubectl apply -f signin-request.yaml
     ```

3. **Approve the CSR**:
   - Approve the CSR using the following command:
     ```bash
     kubectl certificate approve bob-csr
     ```

4. **Retrieve the Signed Certificate**:
   - Retrieve the signed certificate and save it as `bob.crt`:
     ```bash
     kubectl get csr bob-csr -o jsonpath='{.status.certificate}' | base64 -d > bob.crt
     ```

5. **Set Up RBAC**:
   - Apply the `role.yaml` and `rolebinding.yaml` files to configure RBAC:
     ```bash
     kubectl apply -f role.yaml
     kubectl apply -f rolebinding.yaml
     ```

6. **Test Access**:
   - Use the `bob.crt` and `bob.key` to authenticate as `bob` and test access to the Kubernetes cluster.

## Security Notes

- Do not commit sensitive files like `bob.key` to version control. Ensure `.gitignore` is configured to exclude such files.
- Use strong passwords and secure storage for private keys.

## License

This project is licensed under the MIT License. See the LICENSE file for details.


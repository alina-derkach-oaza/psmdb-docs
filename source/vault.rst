.. _vault:

|vault| Integration
=================================================================

|PSMDB| is integrated with |vault|. |vault| supports different secrets engines. |PSMDB| only supports the |vault|
back end with KV Secrets Engine - Version 2 (API)
with versioning enabled.

.. seealso::

    Percona Blog: Using Vault to Store the Master Key for Data at Rest Encryption on |PSMDB|
        https://www.percona.com/blog/2020/04/21/using-vault-to-store-the-master-key-for-data-at-rest-encryption-on-percona-server-for-mongodb/

    How to configure the KV Engine:
        https://www.vaultproject.io/api/secret/kv/kv-v2.html

.. admonition:: |vault| Parameters

   .. list-table::
      :widths: 25 25 15 35
      :header-rows: 1
   
      * - Command line
        - Config file
        - Type
        - Description
      * - vaultServerName
        - security.vault.serverName
        - string
        - The IP address of the Vault server
      * - vaultPort
        - security.vault.port
        - int
        - The port on the Vault server
      * - vaultTokenFile
        - security.vault.tokenFile
        - string
        - The path to the vault token file. The token file is used by |mongodb| to access |vault|. The vault token file consists of the raw vault token and does not include any additional strings or parameters.
             
          Example of a vault token file:

          .. code-block:: text

             s.uTrHtzsZnEE7KyHeA797CkWA

      * - vaultSecret
        - security.vault.secret
        - string
        - The path to the vault secret. Note that vault secrets path format must be:

          .. code-block:: text

             <vault_secret_mount>/data/<custom_path>

          where:

          * ``<vault_secret_mount>`` is your Vault KV Secrets Engine;
          * ``data`` is the mandatory path prefix required by Version 2 API;
          * ``<custom_path>`` is your secrets path

          Example:

          .. code-block:: text

             secret_v2/data/psmdb-test/rs1-27017

          .. note::

             It is recommended to use different secret paths for every database node.
           
      * - vaultRotateMasterKey
        - security.vault.rotateMasterKey
        - switch
        - Enables master key rotation
      * - vaultServerCAFile
        - security.vault.serverCAFile
        - string
        - The path to the TLS certificate file
      * - vaultDisableTLSForTesting
        - security.vault.disableTLSForTesting
        - switch
        - Disables secure connection to |vault| using SSL/TLS client certificates

.. admonition:: Config file example

   .. code-block:: yaml

      security:
        enableEncryption: true
        vault:
          serverName: 127.0.0.1
          port: 8200
          tokenFile: /home/user/path/token
          secret: secret/data/hello

During the first run of the |PSMDB|, the process generates a secure key and writes the key to the vault. 

During the subsequent start, the server tries to read the master key from the vault. If the configured secret does not exist, vault responds with HTTP 404 error.

.. _vault.namespaces:

Namespaces
----------

Namespaces are isolated environments in Vault that allow for separate secret key and policy management. 

You can use Vault namespaces with |PSMDB|. Specify the namespace(s) for the ``security.vault.secret`` option value as follows: 

.. code-block:: bash

   <namespace>/secret/data/<secret_path> 

For example, the path to secret keys for namespace ``test`` on  the secrets engine ``secret`` will be ``test/secret/<my_secret_path>``.

.. note::

   You have the following options of how to target a particular namespace when configuring Vault:

   1. Set the VAULT_NAMESPACE environment variable so that all subsequent commands are executed against that namespace. Use the following command to set the environment variable for the namespace ``test``:

   .. code-block:: bash

      $ export VAULT_NAMESPACE=test

   2. Provide the namespace with the ``-namespace`` flag in commands
 

.. seealso::

   |vault| Documentation: 

   * Namespaces
       https://www.vaultproject.io/docs/enterprise/namespaces
   * Secure Multi-Tenancy with Namespaces
       https://learn.hashicorp.com/tutorials/vault/namespaces


Key Rotation
--------------

Key rotation is replacing the old master key with a new one. This process helps to comply with regulatory requirements.

To rotate the keys for a single ``mongod`` instance, do the following:

1. Stop the ``mongod`` process
#. Add ``--vaultRotateMasterKey`` option via the command line or ``security.vault.rotateMasterKey`` to the config file.
#. Run the ``mongod`` process with the selected option, the process will perform the key rotation and exit.
#. Remove the selected option from the startup command or the config file.
#. Start ``mongod`` again.

Rotating the master key process also re-encrypts the keystore using the new master key. The new master key is stored in the vault. The entire dataset is not re-encrypted.

For a replica set, the steps are the following:

1. Rotate the master key for the secondary nodes one by one.
2. Step down the primary and wait for another primary to be elected.
3. Rotate the master key for the previous primary node.


.. |vault| replace:: HashiCorp Vault
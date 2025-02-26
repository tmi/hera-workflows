# Global Parameters From Configmap Referenced As Local Variable

## Note

This example is a replication of an Argo Workflow example in Hera.
The upstream example can be [found here](https://github.com/argoproj/argo-workflows/blob/master/examples/global-parameters-from-configmap-referenced-as-local-variable.yaml).




=== "Hera"

    ```python linenums="1"
    from hera.workflows import (
        Container,
        Parameter,
        Workflow,
        models as m,
    )

    with Workflow(
        generate_name="global-parameter-from-configmap-referenced-as-local-variable-",
        entrypoint="whalesay",
        arguments=Parameter(
            name="message",
            value_from=m.ValueFrom(config_map_key_ref=m.ConfigMapKeySelector(name="simple-parameters", key="msg")),
        ),
        service_account_name="argo",
    ) as w:
        Container(
            name="whalesay",
            image="argoproj/argosay:v2",
            args=["echo", "{{inputs.parameters.message}}"],
            inputs=Parameter(name="message"),
        )
    ```

=== "YAML"

    ```yaml linenums="1"
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: global-parameter-from-configmap-referenced-as-local-variable-
    spec:
      arguments:
        parameters:
        - name: message
          valueFrom:
            configMapKeyRef:
              key: msg
              name: simple-parameters
      entrypoint: whalesay
      serviceAccountName: argo
      templates:
      - container:
          args:
          - echo
          - '{{inputs.parameters.message}}'
          image: argoproj/argosay:v2
        inputs:
          parameters:
          - name: message
        name: whalesay
    ```


# imagebuilder

This repo is an example to build AutoSD image using Konflux.

Most of the instructions are from [Konflux](https://github.com/konflux-ci/konflux-ci).
In addition, you need to set up the role `ns2-pod-viewer-job-creator` (role given in the onboarding based on the Konflux docs) with:
```
- apiGroups:
  - ""
  resources:
  - persistentvolumeclaims
  - configmaps
  verbs:
  - create
  - delete
  - get
  - list
  - watch
```

In our case, we used `git-clone` ClusterTask, you may switch to the bundle:
```
- name: clone-repository
      params:
      - name: url
        value: $(params.git-url)
      - name: revision
        value: $(params.revision)
      - name: subdirectory
        value: source
      taskRef:
        params:
        - name: name
          value: git-clone
        - name: version
          value: "0.9"
        - name: kind
          value: task
        resolver: hub
```
But, in CRC case, the task needed privileged permissions:
```
securityContext:
        privileged: true
        runAsUser: 65532
```
The deployment in the Konflux docs is for kind cluster, in CRC, lot of permissions needed to be granted per ServiceAccount in order to deploy.

### Notes:
* The reason moving to CRC from Kind cluster is that automotive-image-builder requires loop devices. Using Kind on a VM didn't provide it.

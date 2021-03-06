## Cos'è la CI/CD?
La Continuous Integration/Continuous Delivery (CI/CD) è un approccio strutturato che ha come obbiettivo quello di rendere sicuri, automatizzati ed effortless i processi di testing, compilazione, gestione e rilascio delle applicazioni, in un flusso continuo, denominato `pipeline`.

## Tekton Pipelines
[Tekton Pipelines](!https://github.com/tektoncd/pipeline) è uno framework Open Source sviluppato da Google e nativo Kubernetes, offre la possibilità agli sviluppatori di costruire delle Pipelines che possano operare su più ambienti Cloud ed On-Premise.  
Naturalemente Tekton non opera nell'etere, anch'egli avrà bisogno di risorse ben definite, allocate ed eseguite su Kubernetes.

# Getting Started
Lo scopo di questa demo è quello di costruire un flusso continuo che esegua l'analisi del codice del nostro applicativo, ne validi la buona riuscita e ne

### Requisiti
- [Minikube](!https://kubernetes.io/docs/tasks/tools/install-minikube/)
- [Tekton Pipelines](!https://github.com/tektoncd/pipeline/blob/master/docs/install.md)
- [Tekton Pipelines cli](!https://github.com/tektoncd/cli/releases)

Per prima cosa creiamo un `Task`, questi (i tasks) sono da considerarsi come l'unità atomica di una pipeline, difatti ogniuna di queste è costituita di uno o più tasks che vengono eseguiti in sequenza.  
E' bene cercar sempre di parametrizzare il più possibile un task in modo da poterlo riutilizzare all'occorrenza in futuro cambiando solo i parametri specifici.  
Facciamo un esempio pratico, se ad esempio il nostro task dovrà eseguire il clone di un repository git invece di
```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: clone-repository
spec:
  params:
    - name: RepositoryURL
      type: string
      description: Url of the git repository to clone.
      default: https://github.com/GMH501/redexporter.git
  steps:
    - name: git-clone
      image: alpine/git:latest
      script: |
        git clone  "$(params.RepositoryURL)" codebase
    - name: print-commit
      image: alpine/git:latest
      script: |
        cd codebase && git rev-parse HEAD
```

Eseguendo il comando `kubectl apply -f clone-repository.yaml` creeremo questo oggetto all'interno di Tekton e potremo utlizzarlo a nostro piacimento semplicemente eseguendo da CLI il seguente comando:  
 `tkn task start clone-repository --param=RepositoryURL=https://github.com/GMH501/redexporter.git --showlog`

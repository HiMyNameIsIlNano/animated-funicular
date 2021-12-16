Useful Intellij Template

I use this tiny little groovy script to help me make my TODO comments consistent and look like this: `// TODO: [feature/Ticket-Number] 16.12.2021`:

```groovy
com.intellij.dvcs.repo.VcsRepositoryManager
.getInstance(_editor.project)
.getRepositoryForFileQuick(com.intellij.openapi.fileEditor.FileDocumentManager.getInstance().getFile(_editor.document))
.getCurrentBranchName()
```

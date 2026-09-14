<!-- janitor:begin:todo -->
## TODO

- Reconcile the deprecation notice (`078be12` "moved to oneshot") with subsequent credential commits (`391d328`, `ba7d450`) and the Homelab project contract (`19b2de8`): it is unclear from the commit summaries whether new secrets should still be added in this repository or only in the `oneshot` repository.
- Confirm whether the `Homelab project contract` commit supersedes the deprecation, or whether this repository is retained solely as a secrets store.
- Verify that skills content removed by `d369b9c` now exists in the `oneshot` repository, per that commit subject.
- Check SOPS encryption coverage for oci-dev secrets (`7334a7e`) and the homelab service credentials (`ba7d450`); no verification evidence is available in the supplied documents.
- Both the remote `CONTEXT.md` and `TODO.md` had no surviving non-managed content at `19b2de8`, so no pre-existing task items could be carried forward; this list is inferred from commit subjects only.
<!-- janitor:end:todo -->

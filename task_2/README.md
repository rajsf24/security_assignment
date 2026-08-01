Task 2 — Secure CI/CD Pipeline & Supply Chain
Rebuild the delivery path so security is enforced by the pipeline, not by good intentions.
● Build a GitHub Actions pipeline that builds, scans, signs, and deploys the image (free GitHub runners + GHCR — no cloud account).
● Wire in security gates: SAST (Semgrep), dependency/CVE scan (Trivy/Grype), image scan, and secrets scan (gitleaks).
● Sign the image and generate provenance with Cosign (keyless) + an SLSA-style attestation.
● State each gate’s fail policy — what hard-blocks, what warns, and how you handle a CVE with no fix yet.
● Adopt GitOps with ArgoCD (or Flux) as the source of truth; show drift detection + self-heal after a manual kubectl edit.
Bonus - Upload scanner results as SARIF so they surface in the repo’s Security tab. - cosign verify output proving your image was signed by your workflow. - Canary or blue-green rollout strategy.

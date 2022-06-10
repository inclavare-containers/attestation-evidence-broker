# Intruduction

This patch series is provided to allow the guest application to query AMD SEV(-ES) runtime attestation evidence by communicating with the host service ([Attestation Evidence Broker](../docs/design/design.md)).

In order to work out AEB, we provided this patch series. Here are the workflows:

1. In order to obtain the guest handle required by the `ATTESTATION` command to the SEV firmware, [a new hypercall](0001-KVM-X86-Introduce-KVM_HC_VM_HANDLE-hypercall.patch) needs to be added to the KVM. The guest application obtains the guest handle through this newly added hypercal.

2. The guest application sends the guest handle to the AEB service through VSOCK. The AEB service uses the guest handle as the input parameter of the ATTESTATION command to interact with the SEV firmware. 

3. AEB executes `ioctl` on the `/dev/sev` device and then call the [SEV_GET_REPORT](0003-crypto-ccp-Implement-SEV_GET_REPORT-ioctl-command.patch) interface to send the `ATTESTATION` command to the SEV firmware to query attestation report. To improve code reusability, [this patch](0002-KVM-SVM-move-the-implementation-of-sev_get_attestati.patch) moves the main implementation of sev\_get\_attestation\_report in `kvm/svm` into sev\_do\_get\_report defined in the ccp driver.

# Prerequisites

- Apply these patches to [linux kernel](https://github.com/torvalds/linux).
- Recompile and insmod `kvm_amd` and `ccp` modules.

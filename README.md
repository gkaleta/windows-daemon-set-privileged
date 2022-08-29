# windows-daemon-set-privileged
How to run a Windows daemonset with privileged access in AKS


*******

apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: privileged-daemonset
  namespace: kube-system
  labels:
    app: privileged-daemonset
spec:
  selector:
    matchLabels:
      app: privileged-daemonset
  template:
    metadata:
      labels:
        app: privileged-daemonset
    spec:
      containers:
        - command:
            - pwsh.exe
            - -command
            - |
              $AdminRights = ([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]"Administrator")
              Write-Host "Process has admin rights: $AdminRights"
              while ($true) { Start-Sleep -Seconds 2147483 }
          name: powershell
          image: mcr.microsoft.com/powershell:lts-nanoserver-1809
          securityContext:
            privileged: true
            windowsOptions:
              hostProcess: true
              runAsUserName: "NT AUTHORITY\\SYSTEM"
      hostNetwork: true
      nodeSelector:
        kubernetes.io/os: windows
      terminationGracePeriodSeconds: 0


****

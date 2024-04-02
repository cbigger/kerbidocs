The KServer controls the routing of KMessages through a KerBI's backend. Along with `main.py`, updates to the KServer class generally represent major updates to the KerBI system.

This is because the KServer is the main operating class for a running KerBI. The loading of main.py begins with the creation of a KServer and then proceeds to apply the various component configurations to it. We like to think of it as a KerBI's spine, connecting all the various parts and facilitating their communication.

> [!info]
>The only direct interaction a user can have with the KServer is to send a server restart SYSCALL. You can find the exact JSON for all system controls in the [[SYSCALLS]] section.

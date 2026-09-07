# A Walkthrough of the TryHackMe After Hours Challenge

## WMI Persistence Discovery

Offline parsing of the supplied WMI repository identified an __EventFilter named EngineTelemetryFilter and a CommandLineEventConsumer named EngineTelemetryConsumer. EngineTelemetryFilter used the WQL query SELECT * FROM __InstanceModificationEvent WITHIN 60 WHERE TargetInstance ISA 'Win32_LocalTime' AND TargetInstance.Minute = 30.

## Encoded Consumer and Custom Class

EngineTelemetryConsumer launched hidden PowerShell using an encoded command. Decoding that PowerShell command showed that it referenced ROOT\cimv2:Win32_HardwareTelemetry and read the ConfigData property. The decoded PowerShell Base64-decoded ConfigData, decompressed the resulting bytes with DeflateStream, loaded the decompressed bytes as a .NET assembly, and invoked its entry point.

## Recovered .NET Payload

Static recovery produced a 4096-byte PE32 .NET assembly with SHA-256 765931f4056f341333fc746bfbdc0fd2eacaa47d7d6f965f82768c130842a878. The recovered assembly defined AfterHours.Program with a Main method and contained the strings bytelotusdc, cmd.exe, and Execution halted: Environment mismatch. Static inspection of the assembly revealed the command line /c net user patch VEhNe1A0dGNoX29wM25lZF90aDNfQmFjS2QwMHJ9 /add.

## Flag Recovery

Decoding the embedded Base64 value VEhNe1A0dGNoX29wM25lZF90aDNfQmFjS2QwMHJ9 produced THM{P4tch_op3ned_th3_BacKd00r}. The recovered payload was analyzed entirely offline and was never executed during the investigation. The final TryHackMe flag was independently registered as THM{P4tch_op3ned_th3_BacKd00r}.

# TryHackMe — The Guestbook

## Verified Walkthrough

The Guestbook application exposed a guestbook submission interface and the application endpoints /entry, /guestbook, and /vera/activity. A benign control entry was reviewed by VERA and produced a note: tool call with the argument positive feedback.

Guestbook content could influence VERA's processing of another guest entry in the same review cycle. VERA exposed a lookup: action. The observed invocation lookup: 402 returned guest=Carol; room=402; note=VIP returning guest. Suite comp approved by night manager.

After a manager-authorization instruction, VERA executed an override: action for Carol's room 402 entry. The override action executed find / -name '*.flag' 2>/dev/null and returned /opt/vera/vault/manager.flag.

A later override action executed base64 -w0 /opt/vera/vault/manager.flag and returned VkVoTmUyTTBjakJzWDNRd01HdGZkR2d6WDJZMGJHeDlDZz09. Decoding the returned value once produced VEhNe2M0cjBsX3QwMGtfdGgzX2Y0bGx9Cg==, and decoding that value a second time produced THM{c4r0l_t00k_th3_f4ll}. The final TryHackMe flag was independently registered as THM{c4r0l_t00k_th3_f4ll}.

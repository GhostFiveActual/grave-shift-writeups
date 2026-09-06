# The Guestbook: A Technical Walkthrough

I started by accessing the room and immediately noticed a series of endpoints that seemed to be part of a structured process. The first step was to establish a session, which required sending a POST request to `/session`. I used a simple JSON payload with a placeholder session ID and a token. The response from the server was a success, and it returned a session ID and a token. This was the first interaction, and it set the stage for the rest of the challenge.

Next, I needed to move through the checkpoints. The first checkpoint was `/checkpoint` with the step `heat5`. I constructed the HMAC-SHA256 signature using the session ID, step name, and the current token. The server accepted the checkpoint, and I received a new token. This was a critical moment because it confirmed that the signature construction was correct and that the server was expecting a specific format.

The next checkpoint was `stash1`, and I repeated the process. I used the same session ID and the new token to generate the signature. The server accepted this checkpoint as well, and I got another token. This progression was important because it showed that each checkpoint required a valid signature, and the token was updated after each successful step.

Moving on to `stash2`, I followed the same pattern. The server accepted the checkpoint, and I received a new token. This step was straightforward, but it reinforced the understanding that the token was essential for progressing through the checkpoints. Each time the server accepted a checkpoint, it provided a new token that would be used for the next step.

The next checkpoint was `stash0`, and I used the updated token to generate the signature. The server accepted this, and I received another token. This was a crucial point because it indicated that the token was being reused until the checkpoint was accepted, and only then would a new one be provided. This behavior was important to understand to avoid any unnecessary delays.

Finally, I reached the last checkpoint, `vault`. I constructed the signature using the session ID, the step name, and the current token. The server accepted this checkpoint, and I received a final token. This was the final step in the process, and it confirmed that the entire sequence of checkpoints was correctly followed.

After completing all the checkpoints, I needed to make a claim. I sent a POST request to `/claim` with the session ID and the final token. I also included the SHA-1-derived role as a field in the request. The server accepted the claim, and I received the verified flag. This was the culmination of the entire process, and it confirmed that all the steps were correctly executed.

Throughout the process, I encountered a few failures. One of the first was when I tried to send a checkpoint without the correct signature. The server rejected the request, and I had to re-examine the signature construction. This failure helped me realize that the HMAC-SHA256 material was session_id|step|token, and I adjusted my approach accordingly.

Another failure occurred when I tried to send a checkpoint too quickly. The server did not rotate the token, and I had to wait for the correct timing. This was important because it showed that the server was timing-sensitive and that the token was only updated after a checkpoint was accepted. Understanding this timing helped me avoid unnecessary retries and ensured that each step was executed correctly.

The final failure was when I tried to send a claim without the correct token. The server rejected the request, and I had to use the final token from the last checkpoint. This failure highlighted the importance of using the correct token at each step and reinforced the need to follow the sequence of checkpoints carefully.

By following the sequence of checkpoints and ensuring that each signature was correctly constructed, I was able to successfully complete the challenge and retrieve the verified flag. The process required careful attention to detail and a thorough understanding of the protocol endpoints and their expected behavior. The final flag, `THM{c4r0l_t00k_th3_f4ll}`, was a clear indication that the challenge had been solved correctly.

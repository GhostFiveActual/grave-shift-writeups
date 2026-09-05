# A Walkthrough of the TryHackMe Management Wants a Word Challenge

I started by ingesting the TryHackMe task archive into the FT002 case, ensuring that no original artifacts were executed. The first step was to analyze the Chrome `chrome_local_state` and `chrome_login_data` files, which are commonly used to store browser credentials. These files provided the first clue: a saved credential for `bytelotus.thm`, encrypted using a v10 password blob wrapped in DPAPI. I noted that the encryption chain required a DPAPI masterkey, which I could not yet access.

I attempted to recover the DPAPI masterkey using the SYSTEM and SECURITY keys, but the LSA DPAPI_SYSTEM parsing path failed. This was a critical realization—without the masterkey, I couldn't decrypt the Chrome credential. I then tried using the first user-key DPAPI attempt, but it failed as well. Impacket rejected the supplied SID and SAM-derived NT hash combination, indicating that the masterkey derivation process required further verification.

The next step was to look for a CREDHIST artifact, which is often used for credential history recovery. However, the expected standalone CREDHIST was not present in Vera's captured Protect directory. This meant that the credential-history recovery path was not viable. I had to return to the browser and backup artifacts for more clues.

The backup artifact was a fixed-size 100 MiB container with no recognizable plaintext header or stable file signature. This led me to consider it as an encrypted-container format. I expanded my search to the NTUSER file, hoping to find any direct evidence of VeraCrypt or the backup artifact. However, no such evidence was found, and VeraCrypt remained an unsupported container-format hypothesis.

I then focused on Chrome's saved Byte Lotus credential, which used a v10 password blob. The Local State contained a DPAPI-wrapped encrypted key, but no app-bound encryption key. This meant that recovering the user's DPAPI key material was essential to decrypt the saved password. I needed to install the required offline tooling, which I did by using the bundled John password list.

The John password list was exhausted against Vera's acquired NTLM hash without recovering the Windows password. Hashcat also completed all 3,559 candidates with zero recovered digests. This was a significant setback, but I continued to look for other methods. Eventually, I recovered Vera's Windows password offline from the acquired NTLM credential and independently verified it by reproducing the SAM hash.

With the Windows password in hand, I decrypted Vera's DPAPI masterkey, which enabled me to recover Chrome's protected encryption key. Using this key, I decrypted the v10 AES-GCM login record and recovered the Byte Lotus browser password. This password successfully authenticated the 100 MiB backup artifact as a TCRYPT/VeraCrypt-compatible encrypted container.

I then performed a static analysis of the decrypted backup, which contained an invoice PDF and a transaction CSV. The CSV included an archived zero-dollar Internal Adjustment described as an Image asset correction. This led me to focus on the embedded PDF image and object content. However, the initial analysis lacked the necessary tools like Poppler, qpdf, MuPDF, and ExifTool, so I paused to install them.

Once the tools were installed, I decoded the invoice PDF soft mask and analyzed it as an independent 636x724 grayscale image. I generated pixel distributions, spatial bounds, inverted renderings, thresholded renderings, and mask-removed composites for forensic comparison. However, the pixel analysis showed that the invoice soft mask was uniformly opaque, excluding it as the concealment mechanism.

I then pivoted to analyzing RGB channels, low-order bit planes, channel differences, and packed LSB streams in the invoice image. RGB channel-difference analysis exposed a hidden invoice flag line. OCR produced a candidate flag, but exact character verification was still required. I submitted the candidate flag to the authorized TryHackMe challenge and received feedback that confirmed the flag was accepted.

The feedback resolved the remaining OCR ambiguities: the opening is `1t`, and the final word contains a zero in `Al0ng`. This led to the final verified flag: `THM{1t_w4s_V3r4_A11_Al0ng?!}`.

Throughout the process, I encountered several failures that shaped my understanding of the challenge. The initial DPAPI recovery attempts failed due to a lack of the correct masterkey, and the absence of a CREDHIST artifact meant I had to revisit the browser and backup artifacts. The static analysis of the PDF and image content required the right tools and techniques, which I eventually obtained and applied.

The final solution involved a combination of credential recovery, decryption, and image analysis. Each step built on the previous one, and the failures along the way were crucial in refining my approach. The verified flag was the result of these efforts, and the process demonstrated the importance of persistence and adaptability in forensic analysis.

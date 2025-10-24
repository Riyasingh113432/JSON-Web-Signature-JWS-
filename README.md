# JWS Spring Boot Demo (ES256) - Ready to run

## What this project does
- Signs every JSON HTTP response with **JWS (ES256)** using an in-memory generated P-256 EC keypair at startup.
- Returns every response wrapped as JSON with fields: `data`, `sig`, `kid`, `alg`, `ts`.
- Publishes the public key in JWKS format at `/.well-known/jwks.json` so clients can verify signatures.

## Requirements
- Java 24 (you said you have it)
- Maven (or use your IDE's Maven support)
- VS Code (or any editor)

## How to run
1. Unzip the archive and open the folder in VS Code.
2. From terminal run:
```bash
mvn spring-boot:run
```
3. Call the sample endpoint:
```bash
curl http://localhost:8080/api/hello
```
You will receive a JSON object like:
```json
{
  "data": {"message":"Hello from signed API!","value":42},
  "sig": "<base64url-signature>",
  "kid": "<key id>",
  "alg": "ES256",
  "ts": "2025-10-24T12:34:56Z"
}
```

4. Fetch JWKS (public key):
```bash
curl http://localhost:8080/.well-known/jwks.json
```

## Postman demo / Tamper detection
1. Make a GET request to `http://localhost:8080/api/hello` in Postman.
2. Save the JSON response body.
3. Modify the `data.message` field manually to something else and attempt verification (see verification steps below) — verification must fail.

## Verification (simple Java utility)
A small verifier class is included: `com.example.jws.util.VerifyExample`.
Build the project:
```bash
mvn package
```
Run the verifier and point it at a saved response file or to an HTTP URL returning the wrapper response. It will fetch JWKS from `http://localhost:8080/.well-known/jwks.json` by default.
```bash
java -cp target/jws-springboot-0.0.1-SNAPSHOT.jar com.example.jws.util.VerifyExample /path/to/response.json
```

If `data` is changed, verification will print `Verification result: false`.

## Notes & Limitations
- For demo/ease-of-use this project generates keys at startup. In production you must persist keys (PKCS12 keystore or KMS), rotate keys, protect private keys, and use a deterministic JSON canonicalization strategy when needed.
- The server returns the signature as base64url-encoded `sig` separate from `data`. Production systems often return a compact JWS or detached content approach — this demo follows your acceptance format.

## Files in this archive
- `pom.xml` - Maven build
- `src/main/java/...` - Application code (signing service, advice, controllers)
- `README.md` - this file

---
If you want, I can: generate a PKCS12 keystore and modify the app to load it, or switch to Gradle. Tell me which and I'll update the project.

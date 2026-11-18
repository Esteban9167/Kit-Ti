# 03 · PKI001 — CA interna step-ca

[⬅ Índice Nicolás](00_INDICE_NICOLAS.md) · [Certificados (transversal)](../PREGUNTAS_TRANSVERSALES/07_CERTIFICADOS_Y_CIFRADO.md)

**Ficha:** contenedor `SRVD1DEVPKI001` · `smallstep/step-ca:0.30.2` · DKR001 **10.1.100.41:9000/tcp** · volumen `langustino-pki-data-v2` · `unless-stopped`. Emite certificados para servicios **internos**; no sustituye a Let's Encrypt para lo público.

### PREGUNTA PKI1 · Demuestre que la CA interna funciona
**🗣️ ORAL:** "step-ca corre en DKR001 puerto 9000. Consulto su endpoint de salud por HTTPS validando contra nuestra CA raíz con --cacert: eso demuestra verificación de certificado, no solo que el puerto responde."

**🖥️** `[DKR001]` · **🔑** `ssh dkr1`

**⌨️ (🟢):**
```bash
sudo docker ps --filter name=SRVD1DEVPKI001
sudo docker inspect --format "{{.Name}} | {{.Config.Image}} | {{.HostConfig.RestartPolicy.Name}}" SRVD1DEVPKI001
curl --fail --silent --show-error --cacert "$HOME/langustino/pki001/root_ca.crt" https://10.1.100.41:9000/health
openssl x509 -in "$HOME/langustino/pki001/root_ca.crt" -noout -subject -issuer -dates
```

**✅** `Up (healthy)` · `{"status":"ok"}` · sujeto/emisor/fechas de la raíz.

**⚠️** Si `$HOME/langustino` no existe en tu cuenta: el despliegue está bajo la cuenta de Nicolás → usar captura. **Nunca** abrir claves privadas.

**📌** ✅ emisión y verificación de certificado de prueba.

### PREGUNTA PKI2 · ¿Qué falta?
"Distribuir la raíz a los clientes del dominio con una GPO y emitir el certificado de LDAPS para AD001 con SAN/FQDN. La CSR existe en `C:\LANgustino-Certificados\ad001.req` pero no se firmó: desde AD001 no había TCP 9000 hacia DKR001 (había ping, no TCP)."

### PREGUNTA PKI3 · ¿Por qué separar PKI interna y CA pública?
"La interna reduce el alcance de confianza: solo los clientes que confían expresamente en su raíz aceptan esos certificados. Lo expuesto a Internet necesita una cadena reconocida por cualquier navegador: Let's Encrypt."

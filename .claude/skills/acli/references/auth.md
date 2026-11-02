# Autenticación

`acli` autentica cada producto Atlassian por separado. Este repo solo usa el namespace de Jira —
loguearse ahí NO autentica Confluence ni admin de organización (no hace falta acá, es
solo para que no sorprenda si alguna vez aparece un error `forbidden` al tocar otro producto).

## API Token (el camino recomendado)

Generar el token en https://id.atlassian.com/manage-profile/security/api-tokens — queda guardado
en `.env` como `ATLASSIAN_API_TOKEN`, junto a `ATLASSIAN_EMAIL`.

```bash
echo "$ATLASSIAN_API_TOKEN" | acli jira auth login \
  --site "TU_INSTANCIA.atlassian.net" \
  --email "$ATLASSIAN_EMAIL" \
  --token
```

`--token` siempre lee de stdin — no tiene forma de argumento directo.

## Verificar, cambiar de sesión, cerrar sesión

```bash
acli jira auth status     # muestra la cuenta activa
acli jira auth switch     # si hay más de una sesión guardada
acli jira auth logout
```

Las sesiones persisten entre terminales — una vez logueado, no hace falta repetirlo en cada
sesión nueva de trabajo.

## Por qué no OAuth (`--web`)

`acli jira auth login --web` abre el navegador — funciona, pero en shells remotas (SSH, WSL sin
configuración especial) el callback puede colgarse porque el navegador abre en otra máquina. Se
recomienda siempre API Token por ser el camino más confiable y el mismo que después necesitan las
llamadas REST directas (auditoría de statuses, edición de custom fields).

## Errores comunes

| Error | Causa más probable | Solución |
|---|---|---|
| `unauthorized: use acli jira auth login to authenticate` | Sesión vencida o nunca logueada | `acli jira auth login` de nuevo |
| `--web` nunca termina después de "Accept" | Callback bloqueado (SSH/WSL remoto) | Usar el camino `--token` |
| Token rechazado después de rotarlo | Credencial en caché apunta al token viejo | `acli jira auth logout` y loguear de nuevo |

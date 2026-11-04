# CliniCore — Historial clínico Angular

Frontend Angular 19 standalone con dashboard, CRUD de pacientes, registro de consultas, visualización de historias y agenda de citas. Incluye datos **ficticios** en `localStorage` para demostración.

> IMPORTANTE: NO es apto para datos médicos reales. No cuenta con autenticación, cifrado de datos clínicos, auditoría, backend ni controles regulatorios. `localStorage` es accesible desde el navegador y NO debe almacenar datos sensibles de pacientes.

## Desarrollo local

Requisitos: Node.js 22 y npm.

```bash
npm install
npm start
```

Abra http://localhost:4200

Para generar el frontend: `npm run build`.

## Desplegar en OpenShift (compilación dentro del clúster)

Requiere CLI `oc`, sesión iniciada (`oc login`) y permiso para crear Builds/Deployments/Routes. Desde la raíz del proyecto:

```bash
oc new-project historial-clinico-demo
oc apply -f openshift/buildconfig.yaml
oc start-build historial-clinico-frontend --from-dir=. --follow --wait
```

Reemplace `PROJECT_PLACEHOLDER` en `openshift/deployment-internal.yaml` por el nombre real de su proyecto (por ejemplo `historial-clinico-demo`). Después:

```bash
oc apply -f openshift/deployment-internal.yaml
oc rollout status deployment/historial-clinico-frontend
oc get route historial-clinico-frontend
```

**Alternativa con imagen externa:** construya y publique la imagen Docker en un registro accesible, cambie `IMAGE_PLACEHOLDER` en `openshift/deployment.yaml` por `registro/usuario/imagen:tag`, y ejecute `oc apply -f openshift/deployment.yaml`. Si el registro es privado, configure un `imagePullSecret`.

## Componentes de despliegue

- `Dockerfile`: compilación Angular y servidor Nginx no privilegiado, puerto 8080.
- `nginx.conf`: soporte para rutas SPA y endpoint `/healthz`.
- `openshift/buildconfig.yaml`: BuildConfig binario e ImageStream.
- `openshift/deployment-internal.yaml`: Deployment, Service y Route TLS para registro interno.
- `openshift/deployment.yaml`: alternativa para imágenes externas.

## Próxima fase para producción

Implementar backend Spring Boot, PostgreSQL, identidad OIDC/Keycloak, RBAC, auditoría, TLS extremo a extremo, cifrado y gestión de secretos, respaldos y cumplimiento normativo aplicable en Colombia. El servidor debe controlar la autorización; nunca confiar solo en el frontend.

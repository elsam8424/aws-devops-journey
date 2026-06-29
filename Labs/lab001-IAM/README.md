# Lab 001 — IAM: Gestión de Identidades

## Objetivo

Aprender a crear y gestionar usuarios IAM usando AWS CLI contra Floci.

## Prerequisitos

- Floci corriendo en localhost:4566
- AWS CLI instalado y configurado

## Paso 1 — Verificar Floci (Emulador AWS)

```bash
aws --endpoint-url http://localhost:4566 iam list-users
```

## Resultado paso 1:

```bash
{
    "Users": []
}
```

**Resultado:** Lista vacía — Floci inicia sin usuarios ✓

## Paso 2 — Crear usuario IAM

```bash
aws --endpoint-url http://localhost:4566 iam create-user --user-name samuel-devops-practica
```

**Resultado:**

```json
{
    "User": {
        "Path": "/",
        "UserName": "samuel-devops-practica",
        "UserId": "AIDAXHB3H3BENCA5PI7L",
        "Arn": "arn:aws:iam::000000000000:user/samuel-devops-practica",
        "CreateDate": "2026-06-29T03:18:25.613929+00:00"
    }
}
```

**Nota:** `000000000000` es el account ID de Floci. En AWS real account ID.

## Paso 3 — Verificar usuarios existentes

```bash
aws --endpoint-url http://localhost:4566 iam list-users
```

**Resultado:** 2 usuarios listados — confirma que Floci persiste 
los datos en sesión ✓

```bash
{
    "Users": [
        {
            "Path": "/",
            "UserName": "samuel-devops-practica",
            "UserId": "AIDAXHB3H3BENCA5PI7L",
            "Arn": "arn:aws:iam::000000000000:user/samuel-devops-practica",
            "CreateDate": "2026-06-29T03:18:25.613929+00:00"
        },
        {
            "Path": "/",
            "UserName": "devops-practica2",
            "UserId": "AIDAFT9DHVHL7BYF9653",
            "Arn": "arn:aws:iam::000000000000:user/devops-practica2",
            "CreateDate": "2026-06-29T03:22:05.351373+00:00"
        }
    ]
}
```

**Nota:** `devops-practica2` fue creado como prueba, 
se eliminará en el siguiente paso.

## Paso 4 — Eliminar usuario de prueba

```bash
aws --endpoint-url http://localhost:4566 iam delete-user --user-name devops-practica2
```

**Resultado:** Sin output — comportamiento correcto en AWS. 
El silencio significa éxito ✓

**Verificación:**

```bash
aws --endpoint-url http://localhost:4566 iam list-users
```

**Resultado:**

```bash
{
    "Users": [
        {
            "Path": "/",
            "UserName": "samuel-devops-practica",
            "UserId": "AIDAXHB3H3BENCA5PI7L",
            "Arn": "arn:aws:iam::000000000000:user/samuel-devops-practica",
            "CreateDate": "2026-06-29T03:18:25.613929+00:00"
        }
    ]
}
```

Solo queda `samuel-devops-practica` ✓

## Paso 5 — Asignar política ReadOnlyAccess

```bash
aws --endpoint-url http://localhost:4566 iam attach-user-policy --user-name samuel-devops-practica --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
```
**Resultado:** Sin output = éxito ✓

## Paso 6 — Verificar política asignada

```bash
aws --endpoint-url http://localhost:4566 iam list-attached-user-policies --user-name samuel-devops-practica
```
**Resultado:**
```json
{
    "AttachedPolicies": [
        {
            "PolicyName": "ReadOnlyAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/ReadOnlyAccess"
        }
    ]
}
```
**Nota:** ReadOnlyAccess = usuario puede ver todo pero no modificar nada.
Principio de mínimo privilegio aplicado ✓

## Resumen y aprendizajes

### Ciclo completo practicado

1. **Crear usuario** — `create-user` con nombre descriptivo
2. **Asignar política** — `attach-user-policy` con permisos mínimos necesarios
3. **Verificar** — `list-users` y `list-attached-user-policies` para confirmar estado
4. **Eliminar usuario de prueba** — `delete-user` y verificar con `list-users`

### Conceptos clave aprendidos

- **Usuario IAM sin política = sin acceso a nada** — crear un usuario no le da permisos automáticamente
- **Sin output = éxito en AWS CLI** — comandos como `delete-user` y `attach-user-policy` no devuelven JSON cuando funcionan
- **Principio de mínimo privilegio** — asignar solo los permisos necesarios (ej. `ReadOnlyAccess` en lugar de `AdministratorAccess`)
- **Windows no usa `\` para saltos de línea** — en PowerShell usar `` ` `` (backtick) o escribir el comando en una sola línea
- **Account ID `000000000000` = Floci**, ID real = AWS producción — el ARN revela en qué entorno estás trabajando

### Comandos del lab

```bash
# Listar usuarios
aws --endpoint-url http://localhost:4566 iam list-users

# Crear usuario
aws --endpoint-url http://localhost:4566 iam create-user --user-name samuel-devops-practica

# Eliminar usuario de prueba
aws --endpoint-url http://localhost:4566 iam delete-user --user-name devops-practica2

# Asignar política ReadOnlyAccess
aws --endpoint-url http://localhost:4566 iam attach-user-policy --user-name samuel-devops-practica --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess

# Verificar políticas asignadas
aws --endpoint-url http://localhost:4566 iam list-attached-user-policies --user-name samuel-devops-practica
```

### Próximo lab

**Lab002-S3** — almacenamiento de objetos con S3
Terraform CI/CD Pipeline
Este repositorio contiene un pipeline completo de CI/CD para despliegues de infraestructura con Terraform, incluyendo múltiples herramientas de validación, seguridad y análisis de costos.

🚀 Características del Pipeline

El pipeline incluye las siguientes etapas y herramientas:

📋 Validación y Formato

Terraform Format Check - Verifica que el código siga las convenciones de formato
Terraform Validate - Valida la sintaxis y configuración de Terraform
TFLint - Linter avanzado que detecta errores y mejores prácticas específicas del provider

🔒 Análisis de Seguridad

tfsec - Análisis de seguridad estático específico para Terraform
Checkov - Escáner de políticas de seguridad y compliance

💰 Análisis de Costos

Infracost - Estimación de costos de la infraestructura antes del despliegue

🔄 Despliegue

Terraform Plan - Generación del plan de ejecución
Aprobación Manual - Revisión humana antes del despliegue
Terraform Apply - Aplicación de los cambios aprobados

🛠️ Configuración Inicial

1. Secrets de GitHub
Configura los siguientes secrets en tu repositorio de GitHub:
SecretDescripciónAWS_ACCESS_KEY_IDID de clave de acceso de AWSAWS_SECRET_ACCESS_KEYClave secreta de acceso de AWSINFRACOST_API_KEYAPI key de Infracost (ver configuración abajo)
2. Configuración de AWS
Asegúrate de que las credenciales de AWS tengan los permisos necesarios para:

Leer/escribir en el bucket de S3 para el estado de Terraform (si usas remote state)
Crear y gestionar los recursos definidos en tu configuración de Terraform

3. Configuración de Infracost
Para usar Infracost:

Regístrate en infracost.io
Obtén tu API key desde tu dashboard
Agrégala como secret INFRACOST_API_KEY en tu repositorio de GitHub

📊 Resultados y Reportes

GitHub Security Tab
Los resultados de tfsec y Checkov se cargan automáticamente en la pestaña Security de GitHub, donde puedes:

Ver vulnerabilidades detectadas
Seguir el progreso de correcciones
Obtener recomendaciones detalladas

Comentarios de Infracost
Infracost genera automáticamente comentarios en los Pull Requests con:

Estimación de costos de los nuevos recursos
Comparación con la infraestructura actual
Desglose detallado por servicio

Artefactos del Pipeline

Terraform Plan - Se guarda como artefacto por 5 días para auditoría

🏃‍♂️ Flujo de Ejecución

mermaidgraph TD
    A[Push to main] --> B[Terraform Format Check]
    B --> C[Terraform Validate]
    C --> D[Setup & Run TFLint]
    D --> E[Run tfsec]
    E --> F[Run Checkov]
    F --> G[Generate Infracost Report]
    G --> H[Terraform Plan]
    H --> I[Upload Plan Artifact]
    I --> J[Manual Approval]
    J --> K[Terraform Apply]

🔧 Personalización

Modificar Herramientas de Seguridad
Si deseas que las herramientas de seguridad no detengan el pipeline en caso de fallos:
yaml- name: Run tfsec
  uses: aquasecurity/tfsec-action@v1.0.3
  with:
    soft_fail: true  # Cambiar a true
yaml- name: Run Checkov
  uses: bridgecrewio/checkov-action@master
  with:
    soft_fail: true  # Cambiar a true
Configurar TFLint
Puedes personalizar TFLint creando un archivo .tflint.hcl en la raíz del proyecto:
hclplugin "aws" {
    enabled = true
    version = "0.21.1"
    source  = "github.com/terraform-linters/tflint-ruleset-aws"
}

rule "terraform_unused_declarations" {
  enabled = true
}

rule "terraform_comment_syntax" {
  enabled = true
}
Cambiar Región de AWS
Modifica la región en el archivo del pipeline:
yaml- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    aws-region: us-west-2  # Cambiar región aquí

👥 Aprobadores

Actualiza la lista de aprobadores en el paso de aprobación manual:
yaml- name: Manual Approval
  uses: trstringer/manual-approval@v1
  with:
    approvers: usuario1,usuario2,usuario3  # Lista separada por comas
    minimum-approvals: 2  # Número mínimo de aprobaciones
    
📝 Estructura de Archivos Recomendada
.
├── .github/
│   └── workflows/
│       └── terraform.yml
├── .tflint.hcl
├── main.tf
├── variables.tf
├── outputs.tf
├── README.md
└── modules/
    └── ...
    
🐛 Solución de Problemas

Error: "No se pudo inicializar TFLint"

Verifica que tengas un archivo .tflint.hcl en la raíz del proyecto
O modifica el pipeline para omitir la inicialización si no usas plugins

Error: "Infracost API key no válida"

Verifica que el secret INFRACOST_API_KEY esté configurado correctamente
Asegúrate de que la API key sea válida en tu cuenta de Infracost

Errores de Permisos en AWS

Verifica que las credenciales de AWS tengan los permisos necesarios
Revisa que la región configurada sea correcta

📚 Recursos Adicionales

Documentación de Terraform
TFLint Rules
tfsec Checks
Checkov Policies
Infracost Documentation

🤝 Contribución

Fork el repositorio
Crea una rama para tu feature (git checkout -b feature/nueva-funcionalidad)
Commit tus cambios (git commit -am 'Agrega nueva funcionalidad')
Push a la rama (git push origin feature/nueva-funcionalidad)
Crea un Pull Request

📄 Licencia
Este proyecto está bajo la licencia MIT. Ver el archivo LICENSE para más detalles.

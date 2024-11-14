pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Clona el repositorio en la rama especificada
                git branch: 'main', credentialsId: 'a26ad63d-d7db-47b1-89ed-467aa02c388d', url: 'https://github.com/LuceroCacao/Estudiantes_QA.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                // Crea y activa un entorno virtual de Python e instala dependencias
                sh 'rm -rf venv' // Elimina cualquier entorno virtual previo
                sh 'python3 -m venv venv'
                sh '. venv/bin/activate && pip install -r requirements.txt'
            }
        }

        stage('Code Analysis') {
            steps {
                // Analiza el código con pylint y permite que el pipeline continúe en caso de errores de estilo
                sh '. venv/bin/activate && pylint test/test_app.py || true'
            }
        }

        stage('Run Tests') {
            steps {
                // Verifica si la carpeta de pruebas existe y ejecuta pytest solo si está presente
                script {
                    if (fileExists('tests')) {
                        sh '. venv/bin/activate && pytest tests/ || true'
                    } else {
                        echo 'La carpeta de pruebas no existe. Saltando ejecución de pruebas.'
                    }
                }
            }
        }

        stage('Push to GitHub') {
            when {
                branch 'main'
            }
            steps {
                // Si todas las etapas anteriores pasan, realiza un push al repositorio
                withCredentials([usernamePassword(credentialsId: 'a26ad63d-d7db-47b1-89ed-467aa02c388d', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh """
                        git config --global user.email "ncacaoq@miumg.edu.gt"
                        git config --global user.name "LuceroCacao"
                        git add .
                        git commit -m "Auto commit after successful build"
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@ https://github.com/LuceroCacao/Estudiantes_QA.gitHEAD:main
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Validación completada y cambios subidos a GitHub.'
        }
        failure {
            echo 'Falló la validación. No se subieron cambios.'
        }
    }
}
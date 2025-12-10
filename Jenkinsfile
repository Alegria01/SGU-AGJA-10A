pipeline {
    agent any

    stages {
        // 🆕 NUEVO STAGE: Configura la red y los volúmenes externos
        stage('Configurando Recursos de Docker') {
            steps {
                // Usamos 'bat' porque lo estás usando para Windows.
                // El '|| exit /b 0' asegura que el paso no falle si el recurso ya existe.
                bat '''
                    echo Asegurando que la red 'sgu-net' exista...
                    docker network create sgu-net || exit /b 0

                    echo Asegurando que el volumen 'sgu-volume' exista...
                    docker volume create sgu-volume || exit /b 0

                    echo Asegurando que el volumen 'certbot-conf' exista...
                    docker volume create certbot-conf || exit /b 0
                '''
            }
        }
        
        // El resto de los stages quedan igual, ya que ahora los recursos existen

        // Parar los servicios que ya existen o en todo caso hacer caso omiso
        stage('Parando los servicios...') {
            steps {
                bat '''
                    docker compose -p sgu-agja-10a down || exit /b 0
                '''
            }
        }

        // Eliminar las imágenes creadas por ese proyecto
        stage('Eliminando imágenes anteriores...') {
            steps {
                bat '''
                    for /f "tokens=*" %%i in ('docker images --filter "label=com.docker.compose.project=sgu-agja-10a" -q') do (
                        docker rmi -f %%i
                    )
                    if errorlevel 1 (
                        echo No hay imagenes por eliminar
                    ) else (
                        echo Imagenes eliminadas correctamente
                    )
                '''
            }
        }

        // Del recurso SCM configurado en el job, jala el repo
        stage('Obteniendo actualización...') {
            steps {
                checkout scm
            }
        }

        // Construir y levantar los servicios (Ahora que la red existe, no fallará)
        stage('Construyendo y desplegando servicios...') {
            steps {
                bat '''
                    docker compose up --build -d
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline ejecutado con éxito'
        }

        failure {
            echo 'Hubo un error al ejecutar el pipeline'
        }

        always {
            echo 'Pipeline finalizado'
        }
    }
}
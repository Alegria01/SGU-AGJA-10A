pipeline {
    agent any

    stages {

        // 🛑 DETENER SERVICIOS EXISTENTES
        stage('Parando los servicios...') {
            steps {
                bat '''
                    echo === Deteniendo servicios previos ===
                    docker compose -p sgu-agja-10a down
                    IF %ERRORLEVEL% NEQ 0 (
                        echo No se pudieron detener servicios o no existen. Continuando...
                    )
                '''
            }
        }

        // 🗑 ELIMINAR IMÁGENES ANTERIORES
        stage('Eliminando imágenes anteriores...') {
            steps {
                bat '''
                    echo === Buscando imágenes del proyecto ===

                    for /f "delims=" %%i in ('docker images --filter "label=com.docker.compose.project=sgu-agja-10a" -q') do (
                        echo Eliminando imagen %%i
                        docker rmi -f %%i
                    )

                    echo Eliminación completa (si había imágenes)
                '''
            }
        }

        // 📥 OBTENER CÓDIGO DEL SCM
        stage('Obteniendo actualización...') {
            steps {
                checkout scm
            }
        }

        // 🚀 CONSTRUIR Y DESPLEGAR SERVICIOS
        stage('Construyendo y desplegando servicios...') {
            steps {
                bat '''
                    echo === Construyendo y levantando servicios ===
                    docker compose up --build -d

                    IF %ERRORLEVEL% NEQ 0 (
                        echo ERROR: Falló la construcción o despliegue.
                        exit /b 1
                    )
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

pipeline {
    agent { label 'linuxslave' }

    environment {
      VERSION = VersionNumber([
        versionNumberString: '${MAJOR_MINOR_VERSION}.${BUILD_DATE_FORMATTED, "yyyyMMdd"}.${BUILDS_TODAY}',
        versionPrefix: '',
        worstResultForIncrement: 'FAILURE'
      ]);
    }

    stages {
        stage('Multiple SCM checkout ') {
            steps {
                echo 'SCM checkout..'
                checkout([$class: 'GitSCM', branches: [[name: '${SERVICE_REPO_BRANCH}']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'GitHubCredentials', url: '${SERVICE_REPO_URL}']]])
                checkout([$class: 'GitSCM', branches: [[name: '${BUILDSCRIPTS_REPO_BRANCH}']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: '${BUILDSCRIPTS_DIR}']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'GitHubCredentials', url: '${BUILDSCRIPTS_REPO_URL}']]])
            }
        }

        stage('Setting App Version'){
            steps {
                sh 'echo "$VERSION"';
            }
        }

        stage('Gradle build') {
            steps {
                echo 'Building..'

                sh './gradlew build -x test'
            }
        }
        stage('Tagging') {
            steps {
                echo 'Tagging..'

                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'GitHubCredentials',
                usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                    sh 'echo uname=$USERNAME pwd=$PASSWORD'
                 }

                //git config --global user.name $gitUser
                //git tag -a ${APP_VERSION} -m "Version ${APP_VERSION}"
               // git push https://${gitUser}:${gitPwd}@${SERVICE_REPO_URL##*//}  --tags
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}


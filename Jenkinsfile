node('linuxslave') {
   echo ' Will run on the slave with name or tag specialSlave'

   env.APP_VERSION = VersionNumber([
     versionNumberString: '${MAJOR_MINOR_VERSION}.${BUILD_DATE_FORMATTED, "yyyyMMdd"}.${BUILDS_TODAY}',
     versionPrefix: '',
     worstResultForIncrement: 'FAILURE'
   ]);

   stage('Setting App Version'){
      sh 'echo "$APP_VERSION"';
   }

    stage('Multiple SCM checkout ') {
        echo 'SCM checkout..'
        checkout([$class: 'GitSCM', branches: [[name: '${SERVICE_REPO_BRANCH}']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'GitHubCredentials', url: '${SERVICE_REPO_URL}']]])
        checkout([$class: 'GitSCM', branches: [[name: '${BUILDSCRIPTS_REPO_BRANCH}']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: '${BUILDSCRIPTS_DIR}']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'GitHubCredentials', url: '${BUILDSCRIPTS_REPO_URL}']]])
    }

    stage('Gradle build'){
        sh './gradlew build -x test'
    }

    stage('Latest Changes'){
        echo currentBuild.result
    }

    stage('Tagging') {
        echo 'Tagging..'

        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'GitHubCredentials',
        usernameVariable: 'gitUser', passwordVariable: 'gitPwd']]) {
            sh 'echo uname=$gitUser pwd=$gitPwd'
            def tokens = "${SERVICE_REPO_URL}".split('//')
            def part1 = tokens[0]
            def part2 = tokens[1]

            sh 'git config --global user.name $gitUser'
            sh 'git tag -a ${APP_VERSION} -m "Version ${APP_VERSION}"'
            sh 'git push https://$gitUser:$gitPwd@tokens[1]  --tags'

            //echo 'https://'+${gitUser+':'+${gitPwd}+'@'+part2
         }
    }
}
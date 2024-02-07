nodeLabel = params.NODE_LABEL

def geCurrentFormattedDate() {
    def currentDate = new Date();
    return currentDate.format("yyyy-MM-dd");
}

def constructDockerImageNumber() {
    def currentTime = geCurrentFormattedDate();
    println "currentTime - $currentTime";
    println "${env.BUILD_NUMBER}-${currentTime}";
    return "${env.BUILD_NUMBER}-${currentTime}";
}

dockerImageNumber = constructDockerImageNumber();

node(nodeLabel) {
    stage('incoming') {
        echo params.BRANCH;
    }
    stage('clone repo') {
        /* Let's make sure we have the repository cloned to our workspace */
        checkout scm;
    }
    stage('Building Bull monitor') {
        sh '$(aws ecr get-login --no-include-email --region ap-south-1)'
        sh "docker buildx build --push --tag 669650451927.dkr.ecr.ap-south-1.amazonaws.com/bull-monitor:${dockerImageNumber} -f Dockerfile -o type=image --platform=linux/arm64 ."
    }
    stage('summary') {
        manager.createSummary("https://ci.gopando.in/static/243b2051/images/48x48/search.png").appendText("<div>Deploy : ${dockerImageNumber}</div>", false)
    }
}
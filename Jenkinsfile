def fetchFreeNodeLabel() {
  Jenkins jenkins = Jenkins.instance;
  def jenkinsNodes =jenkins.nodes;
  nodeLabel = null;
  // using risc architecture machines - graviton
  buildNodeLabels = ['graviton_docker': false, 'cl1_label': false, 'cl2_label': false, 'cl3_label': false,'cl4_label': false, 'cl5_label': false, 'cl58_label': false, 'cl153_label': false, 'cl115_label': false, 'cl242_label': false, 'cl251_label': false, 'cl290_label': false];
  for (Node node in jenkinsNodes) {
    nodeLabelString = node.getLabelString();
    if (buildNodeLabels.containsKey(nodeLabelString)) {
      if (!node.getComputer().isOffline())  {
        if(node.getComputer().countBusy()==0) {
          buildNodeLabels[nodeLabelString] = true;
          println "${nodeLabelString} is available";
        } else {
          println "$nodeLabelString is busy !!!";
        }
      } else {
        println "$nodeLabelString is offline !!!";
      }
    }
    /*else {
      println "$nodeLabelString is not aviable in node list !!!"
    } */
  }
  for ( e in buildNodeLabels ) {
    if (nodeLabel == null && e.value == true) {
      nodeLabel = e.key;
      break;
    }
  }
  return nodeLabel;
}

nodeLabel = fetchFreeNodeLabel();
println "Selected node for build - $nodeLabel";

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
    stage('installing packages and building') {
        sh "npm install"
        sh "npm run build"
    }
    stage('Building Bull monitor') {
        sh '$(aws ecr get-login --no-include-email --region ap-south-1)'
        sh "docker buildx build --push --tag 669650451927.dkr.ecr.ap-south-1.amazonaws.com/bull-monitor:${dockerImageNumber} ."
    }
    stage('summary') {
        manager.createSummary("https://ci.gopando.in/static/243b2051/images/48x48/search.png").appendText("<div>Deploy : ${dockerImageNumber}</div>", false)
    }
}
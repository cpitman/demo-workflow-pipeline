stage ("Task 1") {
  hook = registerWebhook()
  
  node {
    writeFile file: 'job.yaml', text: formatJob(env.BUILD_ID, "1", "http://www.example.com", hook.getURL())
    sh "oc create -f job.yaml"
  }

  result = waitForWebhook hook
  echo "Job 1 Result (http://www.example.com): ${result}"
}


stage ("Task 2") {
    parallel branchA: {
      hookA = registerWebhook()
      
      node {
        writeFile file: 'job.yaml', text: formatJob(env.BUILD_ID, "2a", "http://www.google.com", hookA.getURL())
        sh "oc create -f job.yaml"
      }
    
      resultA = waitForWebhook hookA
      echo "Job 2a Result (http://www.google.com): ${resultA}"
    }, branchB: {
      hookB = registerWebhook()
      
      node {
        writeFile file: 'job.yaml', text: formatJob(env.BUILD_ID, "2b", "https://www.google.com", hookB.getURL())
        sh "oc create -f job.yaml"
      }
    
      resultB = waitForWebhook hookB
      echo "Job 2b Result (https://www.google.com): ${resultB}"
    },
    branchC: {
      hookC = registerWebhook()
      
      node {
        writeFile file: 'job.yaml', text: formatJob(env.BUILD_ID, "2c", "http://www.google.cn", hookC.getURL())
        sh "oc create -f job.yaml"
      }
    
      resultC = waitForWebhook hookC
      echo "Job 2c Result (http://www.google.cn): ${resultC}"
    }
}

stage ("Task 3") {
    hookThree = registerWebhook()
      
    node {
      writeFile file: 'job.yaml', text: formatJob(env.BUILD_ID, "3", "http://www.redhat.com", hookThree.getURL())
      sh "oc create -f job.yaml"
    }
    
    resultThree = waitForWebhook hookThree
    echo "Job 3 Result (http://www.redhat.com): ${resultThree}"
}

def formatJob(buildId, id, targetUrl, callbackUrl) {
  """
apiVersion: batch/v1
kind: Job
metadata:
  name: curl-${buildId}-${id}
spec:
  template:
    metadata:
      name: curl-${buildId}-${id}
    spec:
      activeDeadlineSeconds: 60
      containers:
      - name: curl-${buildId}-${id}
        image: 172.30.1.1:5000/myproject/curl:latest
        env:
        - name: TARGET_URL
          value: ${targetUrl}
        - name: CALLBACK_URL
          value: ${callbackUrl}
      restartPolicy: Never
  """
}

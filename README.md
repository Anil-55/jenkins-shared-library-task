# 🧠 What is a Shared Library in Jenkins?

Now imagine this:

- You have **10 Java projects**
- All of them use the **same pipeline steps** (build, test, deploy)

Instead of copying the same script into 10 Jenkinsfiles...

✅ You **write the pipeline logic once** in a shared library  
✅ And each Jenkinsfile just says:  
> “Use that shared logic”

🔁 This avoids repeating the same code 10 times.

---

## 🧱 Structure of a Shared Library

Let’s say you made this folder:

jenkins-shared-lib-java/
├── vars/
│ └── buildApp.groovy # main shared logic


📄 `buildApp.groovy` is where your **common logic** lives.

---

## ✅ What’s Inside `buildApp.groovy`

```groovy
def call(Map config = [:]) {
    pipeline {
        agent any

        parameters {
            string(name: 'ENV', defaultValue: 'dev', description: 'Environment')
            string(name: 'BRANCH', defaultValue: 'main', description: 'Branch name')
            choice(name: 'BUILD_TYPE', choices: ['maven', 'gradle'], description: 'Build tool')
        }

        stages {
            stage('Checkout') {
                steps {
                    git branch: "${params.BRANCH}", url: config.repo
                }
            }

            stage('Build') {
                steps {
                    script {
                        if (params.BUILD_TYPE == 'maven') {
                            sh 'mvn clean install'
                        } else {
                            echo "Gradle not yet supported"
                        }
                    }
                }
            }

            stage('Deploy') {
                steps {
                    echo "Deploying to ${params.ENV}"
                }
            }
        }
    }
}


imple English Explanation
Section	What it Means
def call()	This is the function Jenkins runs when we call buildApp()
parameters {}	Ask user for inputs like env, branch, or build type
stages {}	Pipeline steps: checkout code, build it, deploy it
git branch	Pulls the source code from GitHub
sh 'mvn...'	Runs shell commands to build the app using Maven
echo	Just prints a message (e.g., "Deploying to dev")

✅ What’s in Your App Repo: my-java-app
It has this Jenkinsfile:


@Library('shared-lib-java') _

buildApp(
    repo: 'https://github.com/Anil-55/my-java-app.git'
)
📖 What This Means:
@Library('shared-lib-java') _ → Load your shared library

buildApp(...) → Run the function call() from buildApp.groovy with the repo URL

✅ How It All Connects — Final Picture

[ Jenkins Job ]
      ↓
Reads Jenkinsfile from → [ my-java-app ]
      ↓
Loads shared library → [ jenkins-shared-lib-java ]
      ↓
Executes: buildApp()
      ↓
1. Ask user: ENV? BRANCH? BUILD_TYPE?
2. Pull code from GitHub
3. Build using Maven
4. Print deploy message

This keeps your pipeline clean, reusable, and scalable across projects.


---

### 📌 How to Use

1. Copy the above markdown
2. Go to your GitHub repo (e.g., `jenkins-shared-lib-java`)
3. Click **Add file → Create new file**
4. Name it: `README.md`
5. Paste the content
6. Commit the new file

✅ GitHub will render it beautifully on your repo homepage.

Let me know if you also want a `README.md` template for the `my-java-app` repo!





def branches = [:]

branches["ATH"] = {
    node("docker && highmem") {
        def checkoutGit
        stage("ATH: Checkout") {
            checkoutGit = pwd(tmp:true) + "/athgit"
            dir(checkoutGit) {
                git "https://github.com/jenkinsci/git-plugin.git"
                sh "mvn clean package -DskipTests"
                dir("target") {
                    stash name: "localPlugins", includes: "*.hpi"
                }
                sh "mvn clean"
            }
        }
        def metadataPath = checkoutGit + "/essentials.yml"
        stage("Run ATH") {
            def athFolder=pwd(tmp:true) + "/ath"
            dir(athFolder) {
                runATH metadataFile: metadataPath
            }
        }
    }
}
branches["PCT"] = {
    node("docker && highmem") {
        def metadataPath
        env.RUN_PCT_LOCAL_PLUGIN_SOURCES_STASH_NAME = "localPluginsPCT"
        stage("PCT: Checkout") {
            def checkoutGit = pwd(tmp:true) + "/pctgit"
            dir(checkoutGit) {
                dir("git") {
                    git "https://github.com/jenkinsci/git-plugin.git"
                }
                stash name: "localPluginsPCT", useDefaultExcludes: false
            }
            metadataPath = checkoutGit + "/git/essentials.yml"
        }
        stage("Run PCT") {
            def pctFolder = pwd(tmp:true) + "/pct"
            dir(pctFolder) {
                runPCT metadataFile: metadataPath
            }
        }
    }
}

parallel branches

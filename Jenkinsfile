pipeline {
    agent {
        label 'deb'
    }


    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                sh "cd /srv; rm -rf h2b2"
                git 'https://github.com/jpegleg/h2b2'
                sh "gcc h2b2.c -o h2b2 && cp h2b2 /srv/h2b2__built"
            }
            post {
                success {
                    sh "ls /srv/h2b2__built"
                }
            }
        }
        stage('Tests') {
            steps {
                // test the program
                sh "echo 123123123123 | /srv/h2b2__built > /srv/h2b2__tested"
            }
            post {
                success {
                    sh "grep 000100100011000100100011000100100011000100100011 /srv/h2b2__tested || exit 1"
                }
            }
        }
        stage('Publish') {
            steps {
                // make a tarball for pick up
                sh "tar czvf /srv/h2b2_latest_qa.tgz /srv/h2b2__built /srv/workspace/h2b2_deb_build_pipeline/ && touch /srv/h2b2_pickup.lock"
                sh "rm -f /usr/local/bin/h2b2"
                sh "rm -rf /srv/debbuild/h2b2 >/dev/null; mkdir -p /srv/debbuild/h2b2-2.0.0/DEBIAN/; mkdir -p /srv/debbuild/h2b2-2.0.0/usr/local/bin/ >/dev/null"
                sh "cp /srv/workspace/h2b2_deb_build_pipeline/h2b2.control /srv/debbuild/h2b2-2.0.0/DEBIAN/control"
                sh "cp /srv/workspace/h2b2_deb_build_pipeline/postinst /srv/debbuild/h2b2-2.0.0/DEBIAN/postinst"
                sh "chmod +x /srv/debbuild/h2b2-2.0.0/DEBIAN/postinst"
                sh "cp /srv/h2b2__built /srv/debbuild/h2b2-2.0.0/usr/local/bin/h2b2"
                sh "cd /srv/debbuild/ && tar czvf h2b2-2.0.0.tar.gz h2b2-2.0.0/ && dpkg -b ./h2b2-2.0.0 ./h2b2-2.0.0.deb"
            }
            post {
                success {
                    sh "ls /srv/h2b2_latest_qa.tgz || exit 1"
                    sh "cp /srv/debbuild/*.deb /srv/ && touch /srv/h2b2_deb_pickup.lock"
                }
            }
        }
        stage('deb Tests') {
            steps {
                // test the deb
                sh "bash /srv/debuild h2b2-2.0.0.deb"
            }
            post {
                success {
                    sh "ls -larth /usr/local/bin/h2b2 || exit 1"
                }
            }
        }
    }
}

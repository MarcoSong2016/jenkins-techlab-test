/**
 * A Jenkins DevOps Pipeline that allows concurrent builds
 * with constrained resources
 * requires the "Lockable Resources plugin"
 * Author: Lars Berning
 */

stage('Build') {
  node {
    echo 'Building artifacts...'
    // generate build artifacts, unit tests and code analysis
    sleep 2
    // store build artifacts for consecutive stages (i.e. stash
    // or versioned artifact repository)
    echo 'Building artifacts finished'
  }
}

// milestones are used to discard obsolete build, if a consecutive
// build passes a milestone, it will discard all older builds that
// have not yet passed the milestone
// milestone 1
stage('QA') {
  // we only have one QA environment so to limit concurrency to
  // a single build we can use locks
  // if another build reaches a lock, it will "wait" until the
  // resource is available again
  // inversePrecedence instructs the lock to pick the most recent
  // build first
  lock(resource: 'qaEnvironment', inversePrecedence: true) {
    // this milestone in combination with the inversed lock above
    // discards all queued superseded builds
    // milestone 2
    // run integration and performance testing in parallel
    parallel("Integration Tests": {
      node {
        echo 'Running integration tests...'
        // get build artifacts (i.e. from stash)
        // setup test environment
        // run integration tests
        sleep 5
        // store test results
        echo 'Running integration tests finished'
      }
    }, "Performance Tests": {
        echo 'Running performance tests...'
        // setup and run performance tests
        sleep 10
        // store test results
        echo 'Running performance tests finished'
    })
  }
}

// milestone 3
stage('Staging') {
  lock(resource: 'stagingEnvironment', inversePrecedence: true) {
    // milestone 4
    node {
      echo 'Deployment to staging environment...'
      // setup and deploy staging environment
      sleep 5
      echo 'Deployment to staging environment finished'
    }
    // wait for user verification before pushing to production
    input message: 'Deploy to production?'
  }
}

// milestone 5
stage ('Production') {
  lock(resource: 'productionEnvironment', inversePrecedence: true) {
    node {
      echo 'Deployment to production environment...'
      // setup and deploy production environment
      sleep 5
      echo 'Deployment to production environment finished'
    }
  }
}
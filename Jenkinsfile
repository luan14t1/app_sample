pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh '''references:
  working_directory: &working_directory ~/AT-Portal-BE
  images:
    base_image: &base_image
      image: circleci/ruby:2.3-jessie-node-browsers
      environment:
        PGHOST: 127.0.0.1
        BUNDLE_JOBS: 4
        BUNDLE_RETRY: 3
        BUNDLE_PATH: vendor/bundle
        GEM_PATH: vendor/bundle
        RAILS_ENV: test
        RAILS_DATABASE_USERNAME: postgres
    db_image: &db_image
      image: circleci/postgres:9.5
      environment:
        PGDATA: /dev/shm/pgdata/data
        POSTGRES_DB: rails-api-portal_test
        POSTGRES_USER: postgres

  commands:
    install_libmcrypt: &install_libmcrypt
      name: Install libmcrypt-dev
      command: sudo apt-get install libmcrypt-dev
    move_database_yml: &move_database_yml
      name: Move database_yml
      command: mv config/database.ci.yml config/database.yml
version: 2
jobs:
  build:
    working_directory: *working_directory
    docker:
      - *base_image
    steps:
      - checkout
      - run: *install_libmcrypt
      - run: *move_database_yml
      - run:
          name: Run bundle install
          command: bundle check || bundle install --clean
      - persist_to_workspace:
          root: *working_directory
          paths:
            - ./*
  test:
    parallelism: 2
    working_directory: *working_directory
    docker:
      - *base_image
      - *db_image
    steps:
      - attach_workspace:
          at: *working_directory
      - run: *install_libmcrypt
      - run:
          name: Run bundle rubocop check
          command: bundle exec rubocop -R

      - run:
          name: Wait for DB
          command: dockerize -wait tcp://localhost:5432 -timeout 1m
      - run:
          name: Database setup
          command: bundle exec rake db:create db:migrate --trace
      # Run rspec
      # - run:
      #     name: Run rspec
      #     command: |
      #       bundle exec rspec
      # - type: shell
      #   command: |
      #     bundle exec rake test \\
      #       $(circleci tests glob "test/**/*_test.rb" \\
      #         | circleci tests split --split-by=timings --timings-type=classname)
      - store_test_results:
          path: test_results
workflows:
  version: 2
  build_and_deploy:
    jobs:
      - build
      - test:
          requires:
            - build
'''
      }
    }
    stage('Deploy') {
      steps {
        echo 'abc'
      }
    }
  }
}
# Drupal Deployment into Kubernetes

The purpose of this Drupal codebase is to demonstrate a Drupal project and configuration set up for deployment into Kubernetes.

## Documentation

Please read through the [project documentation](docs/README.md) for details about how this project was created, how it's structured for easy development and deployment into production container environments, and how you can create your _own_ Drupal project like it.

## Local setup

  1. Build the site's docker image from the Dockerfile:

     ```
     docker build -t geerlingguy/drupal-for-kubernetes .
     ```

  1. Run the local development environment:

     ```
     docker-compose up -d
     ```

     (Wait for the environment to come up—you can monitor the logs with `docker-compose logs -f`).

  1. Once the container is running, install Dependencies and install Drupal. You can either access http://localhost/ and install using the UI, or install via Drush:

     ```
     # Install dependencies.
     docker-compose exec drupal composer install

     # Install Drupal.
     docker-compose exec drupal bash -c 'drush site:install minimal --db-url="mysql://drupal:$DRUPAL_DATABASE_PASSWORD@$DRUPAL_DATABASE_HOST/drupal" --site-name="Drupal Example Site for Kubernetes" --existing-config -y'
     ```

  1. Visit http://localhost/ in your browser, and login as `admin` using the password Drush printed in the 'Installation complete' message.

### Managing Configuration

After making any configuration changes on the website, you can export the configuration to disk so it can be preserved in the codebase and deployed to the production site:

    docker-compose exec drupal bash -c 'drush config:export -y'

For more on the way this project's configuration changes are handled, and the general site development process, see the [project documentation](docs/README.md).

### Exporting Content

After making any content changes on the website, you can export the changes to disk so it is preserved in the codebase and installable on the production site:

    docker-compose exec drupal bash -c 'drush dcer --folder=modules/custom/pidramble_default_content/content/ node 1'

For more on the way this project's content changes are handled, see the [pidramble_default_content README](web/modules/custom/pidramble_default_content/README.md).

### Upgrading Core (and Contrib)

  1. Set up the site like normal, make sure it's installed.
  2. Run `docker-compose exec drupal composer update` (to update everything).
  3. Run `docker-compose exec drupal bash -c 'drush updb -y'`
  4. Run `docker-compose exec drupal bash -c 'drush config:export -y'`
  5. Commit any changes and push them.


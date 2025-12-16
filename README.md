# AggieAssign

## What is AggieAssign?
AggieAssign was undertaken as a final project for CSCE
606. This project was solicited by Dr. Bettati, one of the associate heads of
the Texas A&M Computer Science Department. One of his responsibilities
is the formation of the department’s semester course schedule.
This task was previously done manually and required a great deal of effort.
Scheduling demands balancing physical constraints,
such as room occupancy or extra classroom features necessary
for lab courses. There are also soft preferences to consider,
namely the instructor’s affinity for course material and time
slot. The core goal of this project was to algorithmically
generate quality schedules, which could then be tweaked by
hand if needed to generate the final product.

## How does AggieAssign work?
AggieAssign requires the user to submit data about courses, rooms, and professors. This information
is then used to generate happiness scores for each possible assignment. Our scheduling objective
is twofold: minimize room wastefulness and maximize professor happiness. Both of these problems can
be cast as integer linear programs, enabling efficient solutions via external libraries. On real data
collected from the Spring 2024 semester, AggieAssign found a schedule with >80% happiness in under a minute. 
For more details on our algorithm, take a look at the [Final Report](./documentation/Fall2024/AggieAssignReport.pdf).

## Table of Contents
- [Important Links](#important-links)
- [Build Instructions](#build-instructions)
- [Getting Started with Setup and Deployment](#getting-started-with-setup-and-deployment)
   - [Setting up the app locally](#setting-up-the-app-locally)
   - [Prepare the application for Heroku](#prepare-application-for-heroku)
- [Using the Application](#using-the-application)
- [Run Tests locally](#run-tests-locally)
- [Contact Information](#contact)

## Important Links
- **App**: [Live Application](https://faculty-teaching-assignment-31f5f9c405bc.herokuapp.com)
- **Application Demo**: [Application Demo Video](https://youtu.be/020dlN_XJJg)
- **Final Report**: [Final Report](./documentation/Fall2024/AggieAssignReport.pdf)
- **Presentation**: [Presentation](./documentation/Fall2024/Final%20Presentation.pptx)


## Build Instructions

### Prerequisites
- **Ruby**: Version `>= 2.7.0`
- **C Compiler**: Required for native extensions (e.g., `gcc` or `clang`)

Clone repository:
```
git clone git@github.com:tamu-edu-students/Faculty-Teaching-Assignment.git
```
Download gems and other dependencies:
```
cd Faculty-Teaching-Assignment
bundle install
rake glpk:install
```
The schedule builder relies on [GLPK](https://www.gnu.org/software/glpk/), an open-source linear program solver. This is downloaded, configured, and installed by the `glpk:install` Rake task. By default, it is installed to the top-level app directory, as to prevent the user from having to add the required binaries to their path. See `lib/tasks/glpk.rake` for details.


# Getting Started with Setup and Deployment

## Prerequisites
1. **Heroku Account**: [Sign up for a Heroku account](https://signup.heroku.com/).
2. **Heroku CLI**: Download and install the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli).
3. **GitHub Repository**: Ensure you have access to the github repository.
4. **Rails and Ruby**: Check that you have Ruby and Rails installed locally to set up the app.
5. **OAuth Configuration**: Ensure you have Google OAuth API keys

---

## Setting up the app locally
### 1. Clone the GitHub Repository (If Needed)
If you're setting up the project locally, clone the repository:

```bash
git clone https://github.com/tamu-edu-students/Faculty-Teaching-Assignment.git
cd Faculty-Teaching-Assignment
```

---

### 2. Install Dependencies and setup Rails Master Key
To install all required dependencies, run:

```bash
bundle config set --local without 'production' && bundle install
```

Setup your Google OAuth Rails Credentials using Google Developer Console.
### Edit the Credentials

```bash
  EDITOR=vim rails credentials:edit
```

The credentials file will open in the editor.

Add your Google OAuth credentials to the file in the following format. Make sure to maintain the correct indentation and spacing as shown. There should be 2 spaces before `client_id` and `client_secret`, and a space after the colon:

```yaml
   google:
     client_id: your_client_id
     client_secret: your_client_secret
```

*Note: Replace `your_client_id` and `your_client_secret` with your own Google OAuth credentials. Do not include any quotes around the actual credentials.*

After adding your credentials, save the changes and exit the editor.

For a more detailed explantion, see 
[Setup Google OAuth](https://github.com/tamu-edu-students/Google-Auth-Ruby-By-JD?tab=readme-ov-file#setup-google-oauth-on-googles-end) 
and 
[Add OAuth ID and Secret to Rails Credentials](https://github.com/tamu-edu-students/Google-Auth-Ruby-By-JD?tab=readme-ov-file#add-oauth-id-and-secret-to-rails-credentials).

If steps are followed correctly you would have your ```RAILS_MASTER_KEY``` in ```config/master.key```. ** DO NOT COMMIT THIS TO REMOTE! ** By default, the `config/master.key` is included in the `.gitignore`.
<details>
  <summary>Facing issues?</summary>
  You might need to delete the <code>credentials.yml.enc</code> file which in the <code>config/</code> folder and try to configure the master key again
</details>
---

### 3. Set Up Local Database
Run database migrations and set up the database:

```bash
rails db:create
rails db:migrate
rails db:seed 
```

### 4. Run the server (Locally)
```bash
rails server # To run locally
```

### 5. Access the Application
Open your browser and navigate to [http://localhost:3000](http://localhost:3000) to view the app running.

## Prepare Application for Heroku

1. **Add a `Procfile`**: Create a file named `Procfile` in the root directory with the following line:

   ```plaintext
   web: bundle exec puma -C config/puma.rb
   ```

2. **Add the Heroku Postgres Add-on**: Heroku uses PostgreSQL as the default database for Rails apps. Check your `Gemfile` for the `pg` gem. (https://elements.heroku.com/addons/heroku-postgresql)

3. **Environment Variables**: Use `dotenv` for local testing and set variables on Heroku using the CLI.

---

## 5. Deploy to Heroku

1. **Login to Heroku**:

   ```bash
   heroku login
   ```

2. **Create a New Heroku App**:

   ```bash
   heroku create <YOUR_APP_NAME> # Optionally specify an app name
   ```

3. **Add Heroku as a Git Remote**:

   ```bash
   git remote add heroku https://git.heroku.com/<YOUR_APP_NAME>.git
   ```

4. **Push to Heroku**:

   ```bash
   git push heroku main
   ```

5. **Run Database Migrations on Heroku**:

   ```bash
   heroku run rails db:migrate
   ```

---

## 6. Configure Environment Variables on Heroku and add redirect URI

Add the Heroku domain as an authorized redirect URI to your Google OAuth. Example:
```bash
https://<YOUR_APP_NAME>.herokuapp.com/auth/<PROVIDER>/callback
```

Set environment variables on Heroku for any API keys, secrets, or configuration variables, you will have to setup your Google Authentication key :

If you have used ```RAILS_MASTER_KEY``` to encrypt
```bash
heroku config:set RAILS_MASTER_KEY=<YOUR_MASTER_KEY>
```

If the above steps do not work, see [Deploy to Heroku](https://github.com/tamu-edu-students/Google-Auth-Ruby-By-JD?tab=readme-ov-file#deploy-to-heroku) for OmniAuth documentation.

## Using the application
For a pdf version refer to [User Documentation](https://github.com/tamu-edu-students/Faculty-Teaching-Assignment/blob/main/documentation/Fall2024/AggieAssign%20-%20User%20Documentation.pdf)
1. Login using your TAMU Email ID
2. Click on ```Create a New Schedule``` to create one with a name and semester
3. Click on the schedule card to upload required files
    - Sample files are in the ```db/sample``` folder of the project
    - Upload the rooms, courses and instructors csv files
4. Once the files are uploaded, click on ```View Data``` to view the available data for this schedule
5. Click on ```Add Predefined Courses``` to lock any course/instructor to a particular time slot
    - Click on the Lock icon to lock and unclock a particular time slot and room to ensure no courses are scheduled in that slot
    - Click on a particular cell and select a course from the sidebar to assign a course to a particular slot and room
6. Click on ```Generate Remaining``` to generate the schedule
7. If there is an error in the data that is highlighted, such as the number of courses being more than the instructor hours, go back to ```View Data``` and hide courses that don't need to be scheduled
8. Once the schedule is generated, room bookings can be deleted or locked to modify the schedule as needed
9. Click on ```Export``` to download the schedule as a csv

## Run Tests Locally
After cloning and setting up the repository, run tests from the project root:
1. To run the rspec tests, run
   ```bash
   bundle exec rspec
   ```
2. To run the cucumber tests, run
   ```bash
   bundle exec cucumber
   ```

Coverage reports are available in the ```coverage/``` folder. For those interested in contributing to AggieAssign, we require >90% coverage for any new pull requests.

## Contact
In case of any issues or queries, please contact the developers:
* Wahib Kapdi: [wahibkapdi@tamu.edu](mailto:wahibkapdi@tamu.edu)
* Colby Endres: [colby.endres@tamu.edu](mailto:colby.endres@tamu.edu)
* Navya Unnikrishnan: [navya_unni@tamu.edu](mailto:navya_unni@tamu.edu)
* Navya Priya Nandimandalam: [navyapriya_n@tamu.edu](mailto:navyapriya_n@tamu.edu)
* Pavithra Gopalakrishnan: [pgopal719@tamu.edu](mailto:pgopal719@tamu.edu)
* Yuqi Fan: [fan321@tamu.edu](mailto:fan321@tamu.edu)
* Abel Gizaw: [kingofkings441@tamu.edu](mailto:kingofkings441@tamu.edu)

# Unit 6
## Chapter 2: Model Parameters
In this chapter, you will add parameters to the admin and user models.

### New Branch
Enter the command "git checkout -b 06-02-model_params".

### Adding Admin Parameters
* Create admin model tests.  Replace the contents of test/models/admin_test.rb with the following:
```
require 'test_helper'

class AdminTest < ActiveSupport::TestCase
  def setup
    @admin = Admin.new(last_name: 'Bond', first_name: 'James',
                       username: 'jbond007', email: '007@example.com',
                       password: 'bond_james_bond',
                       password_confirmation: 'bond_james_bond',
                       confirmed_at: Time.now)
  end

  test 'should be valid' do
    assert @admin.valid?
  end

  test 'email should be present' do
    @admin.email = '     '
    assert_not @admin.valid?
  end

  test 'email should not be too long' do
    @admin.email = 'a' * 244 + '@example.com'
    assert_not @admin.valid?
    @admin.email = 'a' * 243 + '@example.com'
    assert @admin.valid?
  end

  test 'email validation should accept valid addresses' do
    valid_addresses = %w(admin@example.com ADMIN@foo.COM A_US-ER@foo.bar.org
                         first.last@foo.jp alice+bob@baz.cn)
    valid_addresses.each do |valid_address|
      @admin.email = valid_address
      assert @admin.valid?, "#{valid_address.inspect} should be valid"
    end
  end

  test 'email validation should reject invalid addresses' do
    invalid_addresses = %w(admin@example,com admin_at_foo.org
                           admin.name@example. foo@bar_baz.com
                           foo@bar+baz.com)
    invalid_addresses.each do |invalid_address|
      @admin.email = invalid_address
      assert_not @admin.valid?, "#{invalid_address.inspect} should be invalid"
    end
  end

  test 'email address should be unique' do
    duplicate_admin = @admin.dup
    duplicate_admin.email = @admin.email.upcase
    @admin.save
    assert_not duplicate_admin.valid?
  end

  test 'username should be unique' do
    # duplicate_admin: different email; same username but uppercase
    duplicate_admin = @admin.dup
    duplicate_admin.email = '008@example.com'
    duplicate_admin.username = @admin.username.upcase
    @admin.save
    assert_not duplicate_admin.valid?
    # duplicate_admin: different email; different username
    duplicate_admin = @admin.dup
    duplicate_admin.email = '008@example.com'
    duplicate_admin.username = 'jbond008'
    @admin.save
    assert duplicate_admin.valid?
  end

  test 'username should be present' do
    @admin.username = 'jbond_007'
    assert @admin.valid?
    @admin.username = 'j'
    assert @admin.valid?
    @admin.username = '     '
    assert_not @admin.valid?
  end

  test 'username should not have @ symbol in it' do
    @admin.username = 'jbond007@'
    assert_not @admin.valid?
    @admin.username = '@jbond007'
    assert_not @admin.valid?
    @admin.username = 'jbond007@example.com'
    assert_not @admin.valid?
  end

  test 'username should be no longer than 255 characters' do
    @admin.username = 'a' * 256
    assert_not @admin.valid?
    @admin.username = 'a' * 255
    assert @admin.valid?
  end

  test 'first name should be present and no longer than 50 characters' do
    @admin.first_name = '     '
    assert_not @admin.valid?
    @admin.first_name = 'A' + 'a' * 50
    assert_not @admin.valid?
  end

  test 'last name should be present and no longer than 50 characters' do
    @admin.last_name = '     '
    assert_not @admin.valid?
    @admin.last_name = 'A' + 'a' * 50
    assert_not @admin.valid?
  end

  test 'password should have a minimum length of 10 characters' do
    @admin.password = @admin.password_confirmation = 'a' * 9
    assert_not @admin.valid?
    @admin.password = @admin.password_confirmation = 'a' * 10
    assert @admin.valid?
  end
end
```
* Enter the command "sh testm.sh".  You'll see that these admin model tests fail.
* Update the admin model.  Enter the following commands to add parameters:
```
rails generate migration add_params_to_admins last_name:string first_name:string username:string super:boolean
rails db:migrate:reset
```
* In the (time)_devise_create_admins.rb file, uncomment the lines under "## Confirmable" and "## Lockable" and the lines specifying the confirmation_token and unlock_token.
* Enter the command "rails db:migrate:reset".
* Replace the contents of the app/models/admin.rb file with the following:
```
#
class Admin < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable,
         :confirmable, :lockable, authentication_keys: [:username]

  before_save :downcase_email, :downcase_username

  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\-.]+\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }

  validates :last_name, presence: true, length: { maximum: 50 }
  validates :first_name, presence: true, length: { maximum: 50 }

  VALID_USERNAME_REGEX = /\A[\w+\-.]+\z/i
  validates :username, presence: true, length: { maximum: 255 },
                       format: { with: VALID_USERNAME_REGEX },
                       uniqueness: { case_sensitive: false }

  private

  # Converts email to all lower-case.
  def downcase_email
    self.email = email.downcase
  end

  # Converts username to all lower-case.
  def downcase_username
    self.username = username.downcase
  end
end
```
* Enter the command "sh testm.sh".  You'll see that one test fails, the test for minimum password length.
* Edit the config/initializers/devise.rb file.  Change the value of the config.password_length parameter from "6..128" to "10..128".  Change the value of config.mailer_sender to "somebody@rubyonracetracks.com" (in the interest of consistency with the email address shown on the Contact page).
* Enter the command "sh testm.sh".  You'll see that all model tests pass.
* Enter the command "sh testml.sh".  You'll see that there are no Brakeman, RuboCop, or Rails Best Practices offenses.
* Update the admin test fixtures by replacing the contents of test/fixtures/admins.yml with the following:
```
# Read about fixtures at
# http://api.rubyonrails.org/classes/ActiveRecord/FixtureSet.html

elle_woods: 
  username: 'ewoods'
  last_name: 'Woods'
  first_name: 'Elle'
  email: 'elle_woods@example.com'
  super: true
  encrypted_password: <%= Admin.new.send(:password_digest, 'endorphins') %>
  confirmed_at: <%= Time.zone.now %>

vivian_kensington:
  username: 'vkensington'
  last_name: 'Kensington'
  first_name: 'Vivian'
  email: 'vanessa_kensingston@example.com'
  super: true
  encrypted_password: <%= Admin.new.send(:password_digest, 'nice outfit') %>
  confirmed_at: <%= Time.zone.now %>

emmett_richmond:
  username: 'erichmond'
  last_name: 'Richmond'
  first_name: 'Emmett'
  email: 'emmett_richmond@example.com'
  super: false
  encrypted_password: <%= Admin.new.send(:password_digest, "new representation") %>
  confirmed_at: <%= Time.zone.now %>
  
paulette_bonafonte:
  username: 'pbonafonte'
  last_name: 'Bonafonte'
  first_name: 'Paulette'
  email: 'paulette_bonafonte@example.com'
  super: false
  encrypted_password: <%= Admin.new.send(:password_digest, "Neptune's Beauty Nook") %>
  confirmed_at: <%= Time.zone.now %>
  
professor_callahan:
  username: 'pcallahan'
  last_name: 'Callahan'
  first_name: 'Professor'
  email: 'professor_callahan@example.com'
  super: true
  encrypted_password: <%= Admin.new.send(:password_digest, 'Titanic guy') %>
  confirmed_at: <%= Time.zone.now %>

warner_huntington:
  username: 'whuntington'
  last_name: 'Huntington'
  first_name: 'Warner'
  email: 'warner_huntington@example.com'
  super: false
  encrypted_password: <%= Admin.new.send(:password_digest, 'need someone serious') %>
  confirmed_at: <%= Time.zone.now %>
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Passes admin model tests"
```

### Adding User Parameters
* Create user model tests.  Replace the contents of test/models/user_test.rb with the following:
```
require 'test_helper'

class UserTest < ActiveSupport::TestCase
  def setup
    @user = User.new(last_name: 'Bond', first_name: 'James',
                     username: 'jbond007', email: '007@example.com',
                     password: 'bond_james_bond',
                     password_confirmation: 'bond_james_bond',
                     confirmed_at: Time.now)
  end

  test 'should be valid' do
    assert @user.valid?
  end

  test 'email should be present' do
    @user.email = '     '
    assert_not @user.valid?
  end

  test 'email should not be too long' do
    @user.email = 'a' * 244 + '@example.com'
    assert_not @user.valid?
    @user.email = 'a' * 243 + '@example.com'
    assert @user.valid?
  end

  test 'email validation should accept valid addresses' do
    valid_addresses = %w(user@example.com USER@foo.COM A_US-ER@foo.bar.org
                         first.last@foo.jp alice+bob@baz.cn)
    valid_addresses.each do |valid_address|
      @user.email = valid_address
      assert @user.valid?, "#{valid_address.inspect} should be valid"
    end
  end

  test 'email validation should reject invalid addresses' do
    invalid_addresses = %w(user@example,com user_at_foo.org user.name@example.
                           foo@bar_baz.com foo@bar+baz.com)
    invalid_addresses.each do |invalid_address|
      @user.email = invalid_address
      assert_not @user.valid?, "#{invalid_address.inspect} should be invalid"
    end
  end

  test 'email address should be unique' do
    # duplicate_user: same email but uppercase; different username
    duplicate_user = @user.dup
    duplicate_user.email = @user.email.upcase
    duplicate_user.username = 'jbond008'
    @user.save
    assert_not duplicate_user.valid?
    # duplicate_user: different email; different username
    duplicate_user = @user.dup
    duplicate_user.email = '008@example.com'
    duplicate_user.username = 'jbond008'
    @user.save
    assert duplicate_user.valid?
  end

  test 'username should be unique' do
    # duplicate_user: different email; same username but uppercase
    duplicate_user = @user.dup
    duplicate_user.email = '008@example.com'
    duplicate_user.username = @user.username.upcase
    @user.save
    assert_not duplicate_user.valid?
    # duplicate_user: different email; different username
    duplicate_user = @user.dup
    duplicate_user.email = '008@example.com'
    duplicate_user.username = 'jbond008'
    @user.save
    assert duplicate_user.valid?
  end

  test 'username should be present' do
    @user.username = 'jbond_007'
    assert @user.valid?
    @user.username = 'j'
    assert @user.valid?
    @user.username = '     '
    assert_not @user.valid?
  end

  test 'username should not have @ symbol in it' do
    @user.username = 'jbond007@'
    assert_not @user.valid?
    @user.username = '@jbond007'
    assert_not @user.valid?
    @user.username = 'jbond007@example.com'
    assert_not @user.valid?
  end

  test 'username should be no longer than 255 characters' do
    @user.username = 'a' * 256
    assert_not @user.valid?
    @user.username = 'a' * 255
    assert @user.valid?
  end

  test 'first name should be present and no longer than 50 characters' do
    @user.first_name = '     '
    assert_not @user.valid?
    @user.first_name = 'A' + 'a' * 50
    assert_not @user.valid?
  end

  test 'last name should be present and no longer than 50 characters' do
    @user.last_name = '     '
    assert_not @user.valid?
    @user.last_name = 'A' + 'a' * 50
    assert_not @user.valid?
  end

  test 'password should have a minimum length of 10 characters' do
    @user.password = @user.password_confirmation = 'a' * 9
    assert_not @user.valid?
    @user.password = @user.password_confirmation = 'a' * 10
    assert @user.valid?
  end
end
```
* Enter the command "sh testm.sh".  You'll see that the user model tests fail.
* Update the user model.  Enter the following commands to add parameters:
```
rails generate migration add_params_to_users last_name:string first_name:string username:string
rails db:migrate:reset
```
* Make the users confirmable and lockable.  In the (date)_devise_create_users.rb file, uncomment the lines under "## Confirmable" and "## Lockable" and the lines specifying the confirmation_token and unlock_token.
* Enter the command "rails db:migrate:reset".
* Replace the contents of app/models/user.rb with the following:
```
#
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable,
         :confirmable, :lockable, authentication_keys: [:username]

  before_save :downcase_email, :downcase_username

  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\-.]+\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }

  validates :last_name, presence: true, length: { maximum: 50 }
  validates :first_name, presence: true, length: { maximum: 50 }

  VALID_USERNAME_REGEX = /\A[\w+\-.]+\z/i
  validates :username, presence: true, length: { maximum: 255 },
                       format: { with: VALID_USERNAME_REGEX },
                       uniqueness: { case_sensitive: false }

  private

  # Converts email to all lower-case.
  def downcase_email
    self.email = email.downcase
  end

  # Converts username to all lower-case.
  def downcase_username
    self.username = username.downcase
  end
end
```
* Enter the command "sh testm.sh".  All tests should pass.
* Enter the command "sh testml.sh".  All tests should pass, and there should be no offenses cited by Brakeman, RuboCop, or Rails Best Practices.
* Update the user test fixtures by replacing the contents of /test/fixtures/users.yml with the following:
```
# Read about fixtures at
# http://api.rubyonrails.org/classes/ActiveRecord/FixtureSet.html

connery:
  last_name: 'Connery'
  first_name: 'Sean'
  username: 'sconnery'
  email: 'sean_connery@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'Goldfinger') %>
  confirmed_at: <%= Time.zone.now %>

lazenby:
  last_name: 'Lazenby'
  first_name: 'George'
  username: 'glazenby'
  email: 'george_lazenby@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'ohmss1969') %>
  confirmed_at: <%= Time.zone.now %>

moore:
  last_name: 'Moore'
  first_name: 'Roger'
  username: 'rmoore'
  email: 'roger_moore@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'moonraker') %>
  confirmed_at: <%= Time.zone.now %>

dalton:
  last_name: 'Dalton'
  first_name: 'Timothy'
  username: 'tdalton'
  email: 'timothy_dalton@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'daylights') %>
  confirmed_at: <%= Time.zone.now %>

brosnan:
  last_name: 'Brosnan'
  first_name: 'Pierce'
  username: 'pbrosnan'
  email: 'pierce_brosnan@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'goldeneye') %>
  confirmed_at: <%= Time.zone.now %>

craig:
  last_name: 'Craig'
  first_name: 'Daniel'
  username: 'dcraig'
  email: 'daniel_craig@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'reboot007') %>
  confirmed_at: <%= Time.zone.now %>

blofeld:
  last_name: 'Blofeld'
  first_name: 'Ernst'
  username: 'eblofeld'
  email: 'ernst_blofeld@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'stainless steel') %>
  confirmed_at: <%= Time.zone.now %>
```
* Enter the command "sh git_check.sh".
* Enter the following commands:
```
git add .
git commit -m "Passes user model tests"
git push origin 06-02-model_params
```

### Wrapping Up
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```

omniauth-devise
===============

Install devise gem by add gem 'devise' to Gemfile
```
	$ rails generate devise:install
	$ rails generate devise users
```
Install omniauth-facebook gem the same way as devise
  
Add uid and provider to users by create migration file
```
	$ rails g migration add_columns_to_users uid:string provider:string
```  
Declare the provider in config/initializers?devise.rb
```
   	config.omniauth :facebook, "KEY", "SECRET"
```
  
Add ":omniauthable" and ":omniauth_providers => [:facebook]"

Create "omniauth_callbacks_controller.rb" in "app/controllers/users"
```
	class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
	  def facebook
	    @user = User.from_omniauth(request.env["omniauth.auth"])

   	    if @user.persisted?
     	      sign_in_and_redirect @user, :event => :authentication #this will throw if @user is not activated
     	      set_flash_message(:notice, :success, :kind => "Facebook") if is_navigational_format?
   	    else
     	      session["devise.facebook_data"] = request.env["omniauth.auth"]
     	      redirect_to new_user_registration_url
   	    end
 	  end		
	end
```
Add from_omniauth method in app/models/users.rb
```	
	def self.from_omniauth(auth)
 	  where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
   	    user.email = auth.info.email
   	    user.password = Devise.friendly_token[0,20]
    	  end
	end
```		

---
layout: post
title: "Layers of Abstraction"
date: 2017-06-17
published: true
---

Learning how to organize our code into logical layers of abstraction is critical to ensure developers sanity and scalability. As an example let’s imagine we are setting up a registration flow for a simple forum app. At the beginning the controller code that handles the user registration probably looked like this:

```ruby
class UsersController < ApplicationController
  def create
    @user = User.new(user_params)
    if @user.save
      flash[:success] = "Welcome to Monchitos. Hope you enjoy it!"
      redirect_to posts_path
    else
      flash[:error] = "Sorry your registration could not be completed"
      render :new
    end
  end

  private

  def user_params
    params.require(:user).permit(:email, :name, :user_name)
  end
end
```
After a little while our application grew and so did the requirements of our registration process that now include:

- Send a welcome email

- Checking if the registration comes from an invite link. If so, then the new user should follow the existing user activity and the existing user should be rewarded

- Check if a certain community milestone has been reached (e.g 50,000 users has been subscribed )

All of the sudden, our once minimalist controller action looks like this:

```ruby
class UsersController < ApplicationController
  def create
    @user = User.new(user_params)

    if @user.save
      #Sends the invitation email using the regular Rails mailers
      AppMailer.welcome_email(@user)

      if params[:invitation_token].present?
        invitation = Invitation.find_by(token: params[:invitation_token])
        # each invitation is associated to the user who issued the invitation
        invitation_user = invitation.user

        # the new user now will follow the invitation user
        @user.leaders << invitation_user

        # the new user is now listed as a follower of the inviations user
        invitation_user.followers << @user

        # stales the invitation so that it can't be used twice
        invitation.update_attribute(:token, nil)

        # rewards the invitation_user for bringing more users yei!
        invitation_user.update_points(500)
      end

      # Inform admins that their milestone has been reached
      if User.count == 50000
        admins = User.where(admin: true)
        AppMailer.community_milestone_reached(admins)
      end

      flash[:success] = "Welcome to Monchitos. Hope you enjoy it!"
      redirect_to posts_path
    else
      flash[:error] = "Sorry your registration could not be completed"
      render :new
    end
  end

  private

  def user_params
    params.require(:user).permit(:email, :name, :username)
  end
end
```

To understand what’s wrong with this code we should read the following paragraph:

> The term automotive was created from Greek autos (self), and Latin motivus (of motion). In 1929 before the Great Depression, the world had 32,028,500 automobiles in use. Automobiles and other motor vehicles have to comply with a certain number of norms and regulations.

The lines above are probably grammatically correct and we can infer that they talk about the automotive industry. But the paragraph as a whole lacks coherence or consistency because it tries to convey too many ideas in one spot.

Our code has a similar problem. It tries to check off all the requirement in a single method!!. This is wrong for several reasons:

- Violates the concerns of the controller whose main job is to re-route or render information to the user.

- It is hard to test due to the amount of path and logic involved.

- Most importantly it is hard for developers to read and reason about.

A “better” way to structure this code could be:

```ruby
class UsersController < ApplicationController
  def create
    @user = UserSignupManager.perform(user_params, invitation_token)

    if @user.errors.blank?
      flash[:success] = "Welcome to Raysurfing. Hope you enjoy it!"
      redirect_to posts_path
    else
      flash[:error] = "Sorry there was an error with your regitration"
      render :new
    end
  end

  private

  def user_params
    params.require(:user).permit(:email, :name, :username)
  end

  def invitation_token
    params[:invitation_token]
  end
end
```

```ruby
class UserSignupManager
  attr_reader :invitation_token, :status, :user

  def initialize(user_params, invitation_token)
    @user = User.new(user_params)
    @invitation_token = invitation_token
  end

  def self.perform
    new(user_params, params[:invitation_token]).perform
  end

  def perform
    begin
      persistance_actions
      mailer_actions
      user
    rescue ActiveRecord::RecordInvalid => e
      user.errors[:base] << "#{e}"
      user
    end
  end

  private

  def persistance_actions
    ActiveRecord::Base.transaction do
      @user = user.save
      InvitationHandler.handle(user, invitation_token)
    end
  end

  def mailer_actions
    MilestoneChecker.check
    AppMailer.welcome_email(@user)
  end
end
```

```ruby
class InvitationHandler
  attr_reader :user, :invitation_token

  def initialize(user, invitation_token)
    @user = user
    @invitation_token = invitation_token
  end

  def self.handle
    new.(user, invitation_token).handle
  end

  def handle
    return unless invitation_token

    follow_relationships
    stale_invitation
  end

  private

  def follow_relationships
    user.leaders << invitation_user
    invitation_user.followers << user
    invitation_user.update_points(500)
  end

  def stale_invitation
    invitation.update_attribute(:token, nil)
  end

  def inivitation_user
    @inivitation_user ||= Invitation.find_by(token: params[:invitation_token]).user
  end
end
```

```ruby
class MilestoneChecker
  def self.check
    return unless User.count == 50000

    admins = User.where(admin: true)
    AppMailer.community_milestone_reached(admins)
  end
end
```

This is a lot more code and a lot more indirection, the code that was in 13 lines files is now spread out into 4 different files.

So Why is it worth it go through all this trouble? For the same reason we do not write a history essay in one paragraph: The concepts we are trying to express now form part of a cohesive story with each class having a well defined purpose:

- UserSignupManager is a higher level class that manages the signup flow and provides error handling.

- InvitationHandler handles the the follows relationships and invitations.
MilestoneChecker checks for milestones.

Other practical advantages are:

- Testing becomes much easier because we have modular classes with well defined inputs and outputs, and a reduced amount of logic in them. Consequently, individual test file sizes also shrink.

- We are able to more easily tackle and recognize edge-cases. Imagine how messy would have been if we have tried to handle persistance errors within the controller action.

- We have established some patters that are more suitable for scaling our application up. If anyone needs to add a persistance action, change the behavior of the follows relationships or simply add a new milestone they can just go straight into the corresponding methods and make their changes.

I hope you enjoyed the reading. Happy coding!

**NOTE**: This article was originally posted [here](https://medium.com/@eduardopoleo/layers-of-abstraction-5302d6d79317)
---
layout: post
title: "Validating has_and_belongs_to_many Associations with Rails 3"
date: 2013-02-03 10:38
comments: true
categories:
---
This week I had to deal with validating a `has_and_belongs_to_many` association. Unfortunately I had to realize that ActiveRecord is not able to properly validate these kind of associations. Even when validation fails, associations are stored in the database. However ActiveRecord can be adjusted with the [deferred_associations gem](https://github.com/MartinKoerner/deferred_associations) to prevent invalid associations being persisted.

<!-- more -->

In case of two models that have a n:m relationship, e.g groups and users, you can create a join table for storing associations between groups and users and use the [has_and_belongs_to_many macro](http://apidock.com/rails/ActiveRecord/Associations/ClassMethods/has_and_belongs_to_many) to make ActiveRecord manage this join table.

In the following example a custom validation is defined to ensure a group does not consist of more than 10 users.

```ruby app/models/group.rb
class Group < ActiveRecord::Base
  has_and_belongs_to_many :users
  validate :has_ten_or_less_users

  private
  def has_ten_or_less_users
    errors.add(:users, 'not more than 10 users allowed') if users.size > 10
  end
end
```

At first glance the validation seems to do the job and the following spec is passing:
```ruby spec/models/group_spec.rb
require 'spec_helper'

describe Group do
  describe '#users' do
    context 'when assigning to one user' do
      before { subject.users << User.create }
      it { should be_valid }
    end

    context 'when assigning to more than 10 users' do
      before do
        11.times { subject.users << User.create }
      end
      it { should_not be_valid }
    end
  end
end
```

But after assigning 11 users to a group there will be 11 entries persisted in the join table, although the validation fails. This is because ActiveRecords inserts the references into the join table right after assignment before any validations are tested. The following spec is not passing any more:

```ruby spec/models/group_spec.rb
require 'spec_helper'

describe Group do
  describe '#users' do
    context 'when assigning to one user' do
      before { subject.users << User.create }
      it { should be_valid }
    end

    context 'when assigning to more than 10 users' do
      before do
        subject.save
        11.times { subject.users << User.create }
      end
      it { should_not be_valid }

      specify 'associations should not be stored' do
        # reload group from DB to make sure that
        # invalid associations have not been stored
        subject.reload
        expect(subject.users).not_to have(11).users
      end
    end
  end
end
```

<pre>
Failures:

  1) Group#users when assigning to more than 10 users associations should not be stored
     Failure/Error: expect(subject.users).not_to have(11).users
       expected target not to have 11 users, got 11
     # ./spec/models/group_spec.rb:21:in `block (4 levels) in <top (required)>'
</pre>

After investigating this issue I found a [proposal for a solution](http://mattberther.com/2012/09/09/validating-habtm-relationships-with-rails-3x) and a [gem](https://github.com/TylerRick/has_and_belongs_to_many_with_deferred_save) that try to fix it. Neither of them worked in our Rails 3.2 environment. The last commit to the gem was 3 years ago and Rails 3 seems not to be supported. However [MartinKoerner](https://github.com/MartinKoerner) extended the legacy gem and provides a gem that does the job for Rails 3: [deferred_associations](https://github.com/MartinKoerner/deferred_associations).

Simply add `gem 'deferred_associations'` into your Gemfile and change `has_and_belongs_to_many` to `has_and_belongs_to_many_with_deferred_save` in your model class:

```ruby app/models/group.rb
class Group < ActiveRecord::Base
  has_and_belongs_to_many_with_deferred_save :users
  validate :has_ten_or_less_users

  private
  def has_ten_or_less_users
    errors.add(:users, 'not more than 10 users allowed') if users.size > 10
  end
end
```

Et voilà, all examples are passing:

<pre>
Group
  #users
    when assigning to one user
      should be valid
    when assigning to more than 10 users
      should not be valid
      associations should not be stored

Finished in 0.15135 seconds
3 examples, 0 failures
</pre>
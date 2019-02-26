```ruby
require 'rails_helper'

RSpec.feature "Users", type: :feature, js: true do

  context "create new user" do

    before(:each) do
      visit new_user_path

      within('form') do
        fill_in 'First name', with: 'Bob'
        fill_in 'Last name', with: 'Squarepants'
        # fill_in 'Email', with: 'bob@sq.com'
        fill_in 'Password', with: 'test123'
        fill_in 'Password confirmation', with: 'test123'
      end
    end

    scenario "should be successful" do

      within('form') do
        fill_in 'Email', with: 'bob@sq.com'
      end

      click_button 'Register'
      expect(page).to have_content('Logged in as bob@sq.com')
      save_screenshot
    end

    scenario "should fail without an email" do

      click_button 'Register'
      expect(page).to have_content("Email can't be blank")
      save_screenshot
    end

  end

end

```

```ruby

RSpec.feature "Carts", type: :feature, js: true do

  context "managing cart" do

      before(:each) do

      @category = Category.create! name: 'Apparel'

      10.times do
        @category.products.create!(
          name: Faker::Hipster.sentence(1),
          description: Faker::Hipster.paragraph(2),
          image: open_asset('apparel1.jpg'),
          price: Faker::Number.between(1000,10000),
          quantity: Faker::Number.between(1,10)
        )
      end
    end


    it "can increase a product quantity" do
      visit root_path
      click_link("Add", match: :first)
      visit cart_path
      save_screenshot
      click_link('+')
      td = find('td:nth-child(4)')
      expect(td.text).to eql('- 2 +')
      save_screenshot
    end

    it "can create order without being logged in" do

      first('article.product').find_link('Add').click

      visit '/cart'

      first('button.stripe-button-el').click

      within_frame 'stripe_checkout_app' do
        fill_in placeholder: 'Card number', with: '4242424242424242'
        fill_in placeholder: 'MM / YY', with: "01/#{Date.today.year + 1}"
        fill_in placeholder: 'CVC', with: '123'

        click_button 'Pay'
      end

      expect(page).to have_content 'Thank you for your order!'

    end

  end


end

```

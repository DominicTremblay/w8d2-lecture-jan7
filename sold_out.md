```ruby
require 'rails_helper'

RSpec.feature "SoldOuts", type: :feature, js:true do

  context "a product with zero quantity" do

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

    scenario "it is sold out" do
        product=Product.last
        product.quantity = 0
        product.save

        visit root_path

        expect(page).to have_css('.sold-out')
        save_screenshot
    end

    scenario "all are sold out" do
        products = Product.all

        products.each do |product|
          product.quantity = 0
          product.save
        end

        visit root_path
        expect(page).to have_css '.sold-out', count: 10
        save_screenshot

    end
  end
end
```

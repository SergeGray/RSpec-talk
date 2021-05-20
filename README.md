# RSpec-talk

## TDD - Test Driven Development
This is a programming style where the tests are written as you write the code.

1. Write a test
2. Test fails
3. Write code to make the test pass
4. Test passes
5. Refactor code
6. Repeat

We already sort of do this, just not in the right order.

## BDD - Behavior Driven Development

Similar to TDD, but includes extra steps to communicate with the non-technical participants of the project. It can be useful when writing site functionality in collaboration with somebody who isn't very familiar with the codebase. Integration tests are almost integral to BDD.

### Unit tests

Testing individual components of the software. Some people like to test every single thing possible.
* We don't have any view tests, it is debatable whether they are needed or not.
* Since we don't have any integration tests, it might be beneficial to test more stuff in the controllers (instance variable assignment, template rendering) via rails-controller-testing gem.
```ruby
get :action
assert_equal assigns(:variable), Model.last
assert_template 'view/action`
```
* Something that we can improve on is unit test isolation. We don't want to test the same stuff multiple times in different places.

### Integration tests

Testing combined components of the software. Integration tests are something we can use more of. With RSpec nested context I will describe later, it is possible to make a tree-like structure of scenarios.

Outside of integration tests, our test coverage isn't bad, but there's certainly room for improvement. Some parts of the code with heavy interaction with third party API aren't covered by tests (`/app/models/candidate_interview.rb` for example).

## RSpec

RSpec is a Ruby testing framework. It's a domain specific language (DSL) - not pure Ruby unlike MiniTest.

Pros:

* Syntax is very easy to understand
```ruby
expect { post :action, params: question_params }.to change { Question.count }
  .and change('Answer.count').by(2)
  .and change(Comment, :count).from(1).to(4)
```
* Nested context
```ruby
describe "GET #action" do
  before { get :action }

  it { is_expected.to respond_with :success }

  it { is_expected.to render_template :action } # render_template is part of the rails-controller-testing gem
end
```
* Comes out the box with support for most tools you might need
  * Mocks, stubs, and doubles
  ```ruby
  Model.stub(:method).with(true)
  
  allow(Model).to receive(:method).with(true).and_return(false)
  expect(Model).to receive(:method).with(true).and_return(false)
  
  model = double("Model", id: 1, name: "Generic Name")
  ```
  * Shared tests
  ```ruby
  require "rails_helper"
  
  RSpec.shared_examples_for "Generic action" do
    it "returns 401 if not authorized" do
      public_send method, path, headers: headers
      expect(response.status).to be_empty
    end
  end
  ```
  ```ruby
  require "rails_helper"

  describe 'Controller', type: :controller do
    describe "GET #action" do
      it_behaves_like "Generic action"
      
      ...
    end

    describe "POST #action" do
      it_behaves_like "Generic action"
      
      ...
    end
  end
  ```
  * Lazy and eager loaded helper methods
  ```ruby
  let(:question) { create(:question) }
  let!(:answer) { create(:answer) }
  ```
  * Hooks (before, after, around)
  ```ruby
  around do |example|
    puts "before example"
    example.run
    puts "after example"
  end
  ```
  * Subject (object that you run tests against)
  ```ruby
  subject { 5 + 5 }
  
  it { is_expected.to eq(10) }
  ```
* Has adapters for most of the testing gems (capybara, webmock, rails-controller-testing, etc)

Cons:

* Takes longer to learn since it's not pure Ruby
* It's way larger than minitest (implementation spans across 3 gems)
* Tests take longer and leave a larger memory footprint - I was unable to find more info just how much slower, but signs point to not too much slower.

## Conclusion

RSpec has a plethora or extremely useful functionality, makes the tests easier to organize, and is enjoyable to work with. MiniTest is a more barebones framework written on pure Ruby, and allows us to do most of the stuff RSpec can do, just with extra steps. Ultimately, it would be nice to use, but not necessary to use.

# RSpec-talk

## TDD

1. Write a test
2. Test fails
3. Write code to make the test pass
4. Test passes
5. Refactor code
6. Repeat

We already do this, just not in the right order.

### BDD

Includes extra steps to communicate with the non-technical participants. It can be useful when writing site functionality in collaboration with somebody who isn't very familiar with a codebase.

### Unit tests

Testing individual components of the software. Some people like to test every single thing possible.
  * We don't have any view tests, it's debatable whether they are needed or not.

Something that we can improve on is unit test isolation. We don't want to double test stuff that we have tested in other places.

### Integration tests

Testing combined components of the software. We could use more of these. With RSpec nested context, it is possible to make a tree-like structure of scenarios.

Outside of integration tests, our test coverage isn't bad, but there's certainly room for improvement. Some parts of code with heady interaction with third party API aren't covered by tests (`/app/models/candidate_interview.rb` for example).

## RSpec

RSpec is a Ruby testing framework. It's a domain specific language (DSL) - not pure Ruby unlike MiniTest.

Pros:

* Syntax is very easy to understand
```ruby
expect { post :action }.to change { Question.count }
  .and change('Answer.count').by(2)
  .and change(Comment, :count).from(1).to(4)
```
* Nested context
```ruby
describe "GET #action" do
  before { get :action }

  it { is_expected.to respond_with :success }

  it { is_expected.to render_template :action }
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
      do_request(method, path, headers: headers)
      expect(response.status).to be_empty
    end
  end
  ```
  ```ruby
  require "rails_helper"

  describe 'Controller', type: :controller do
    describe "GET #action" do
      it_behaves_like "Generic action"
    end

    describe "POST #action" do
      it_behaves_like "Generic action"
    end
  end
  ```
  * Lazy and eager loaded helper methods
  ```ruby
  let(:question) { create(:question) }
  let!(:answer) { create(:answer) }
  ```
  * Hooks (before, after, around)
  * Subject (object that you run tests against)

Cons:

* Takes longer to learn since it's not pure Ruby.
* It's way larger than minitest (multiple gems).
* Tests take longer and leaves a larger memory footprint.

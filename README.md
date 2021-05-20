# RSpec-talk

## TDD

1. Write a test
2. Test fails
3. Write code to make the test pass
4. Test passes
5. Refactoring
6. Repeat

### BDD

Includes extra steps to communicate with the non-technical participants.

### Unit tests

Testing individual components of the software. Some people like to test every single thing possible.

### Integration tests

Testing combined components of the software. We could use more of these.

## RSpec

RSpec is a Ruby testing framework. It's a domain specific language (DSL) - not pure ruby unlike minitest.

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

  it "returns success" do
    expect(response).to be_success
  end

  it "renders action template" do
    expect(response).to render_template(:action)
  end
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

Cons:

* Takes longer to learn since it's not pure Ruby.
* It's way larger than minitest (multiple gems).
* Tests take longer and leaves a larger memory footprint.

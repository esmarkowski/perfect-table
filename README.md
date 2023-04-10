## Perfect Table

Perfect Table is a simple and easy-to-use gem for managing tables in Rails applications. It makes it easy to create sortable, paginated, and searchable tables with minimal boilerplate code. It is designed to work seamlessly with Rails applications and follows the standard Rails conventions.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'perfect_table'
```

And then execute:

```bash
bundle install
```
Or install it yourself as:

bash
Copy code
gem install perfect_table
Usage

### 1. Include PerfectTable in your controller
Include the PerfectTable module in your desired controller:

```ruby
class AccountsController < ApplicationController
  include PerfectTable
  # ...
end
```

### 2. Set up your table in your view
```slim

div#accounts-table[
         data-controller="perfect-table"
         data-perfect-table-url-value="#{list_admin_accounts_path}"]

  table.table.table-hover.table-responsive.table-centered[
           data-length-change="false"
           data-perfect-table-target="table"]
      thead
        tr.text-nowrap
            th= t('terms.name')
            th[data-sort="email"]= t('terms.email')
            th= t('.subscriptions')
            th[data-sort="created_at"]= t('.signed_up')
            th= t('terms.integrations')
            th[data-no-sort] Actions
      tbody#accounts
```

### 3. Implement the action in your controller
```ruby
def index
  sort_by = perfect_table_params[:sort]&.presence_in(PerfectTable::DEFAULT_SORT_FIELDS) || "name"
  sort_direction = perfect_table_params[:direction]&.presence_in(PerfectTable::DEFAULT_SORT_DIRECTIONS) || "asc"

  @accounts = Account.all
                .page(perfect_table_params[:pae])
                .per(perfect_table_params[:per]
                .order(sort_direction)

  render turbo_stream: turbo_stream.update('accounts', partial: 'table')
end
```

### 4. Add search functionality (optional)

```slim
div#accounts-table[
         data-controller="perfect-table"
         data-perfect-table-url-value="#{list_admin_accounts_path}"]

  .d-flex.justify-content-end
    div
      = simple_form_for :search, url: list_admin_accounts_path, method: :get, class: 'col-3 float-end', wrapper: :input_group do |f|
        = f.input :q, placeholder: "Search...", prepend: '<i class="mdi mdi-magnify search-icon "></i>', label: false

  table.table.table-hover.table-responsive.table-centered[
           data-length-change="false"
           data-perfect-table-target="table"]
      thead
        tr.text-nowrap
            th= t('terms.name')
            th[data-sort="email"]= t('terms.email')
            th= t('.subscriptions')
            th[data-sort="created_at"]= t('.signed_up')
            th= t('terms.integrations')
            th[data-no-sort] Actions
      tbody#accounts
```

Using Searchkick

```ruby
def index
  @account = Account.search(perfect_table_params.fetch(:search, '*'), 
    order: {sort_by => sort_direction},
    per_page: perfect_table_params[:per],
    page: perfect_table_params[:page],
  )
  
  render turbo_stream: turbo_stream.update('accounts', partial: 'table')

end

```

### Customization

You can override the perfect_table_params method in your controller to customize the permitted parameters and default configurations. For example:

```ruby
class AccountsController < ApplicationController
  include PerfectTable

  private

  def perfect_table_params
    params.permit(:sort, :direction, :per, :page, search: [:q, :custom_field]).tap do |calculated_params|
      search_query = calculated_params.d
      search_query = calculated_params.dig(:search, :q)
      calculated_params[:search] = search_query.blank? ? '*' : search_query
    end
  end
end
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and the created tag, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/[USERNAME]/perfect-table.

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

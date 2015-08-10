---
title: 'Rails 4 and Jquery Datatables: Converting Scaffold Table to Datatables'
author: Ygam Retuta
layout: post
permalink: /rails-4-jquery-datatables-scaffold-to-datatables/
dsq_thread_id:
  - 3539270374
categories:
  - Uncategorized
---
### VERSIONS:

Rails 4.2

### ASSUMPTIONS:

scaffold: Business with name field

> #### Gemfile

    gem 'jquery-datatables-rails', github: 'rweng/jquery-datatables-rails'
    gem 'ajax-datatables-rails'
    

> #### (run in bash)

    rails generate jquery:datatables:install
    

If the corresponding stylesheet import is unsuccessful, add these to your css manifest:

> #### app/assets/stylesheets/application.css.scss

    *= require dataTables/jquery.dataTables
    

**Notes**: I cannot get @import to work with the jquery-datatables-rails gem. If you somehow are able to, please inform me in the comments and I&#8217;ll make sure to update this.

> #### app/controllers/businesses_controller.rb

    def index
      @businesses = current_user.businesses
      @user = current_user
    
        respond_to do |format|
          format.html
          format.json { render json: BusinessDatatable.new(view_context, { user: current_user }) }
        end
      end
    

**NOTES**: to provide additional data which will be available as an `options` hash in the datatable, add a hash to the instantiation of the datatable in the controller response.

> #### app/assets/javascripts/business.coffee

    $ ->
      $('#businesses_table').dataTable
        processing: true
        serverSide: true
        ajax: $('#businesses_table').data('source')
        pagingType: 'full_numbers'
        aoColumnDefs: [ {
          bSortable: false,
          aTargets: ["no-sort"]
        } ]
    

> #### (type in bash)

    rails generate datatable Business
    

> #### app/datatables/business_datatable.rb

    class BusinessDatatable < AjaxDatatablesRails::Base
    
      def_delegators :@view, :link_to, :user_business_path, :edit_user_business_path
    
      def sortable_columns
        @sortable_columns ||= %w(Business.name)
      end
    
      def searchable_columns
        @searchable_columns ||= %w(Business.name)
      end
    
      private
    
      def data
        records.map do |record|
          [
            link_to(record.name, user_business_path(options[:user], record)),
            link_to('Show', user_business_path(options[:user], record)),
            link_to('Edit', edit_user_business_path(options[:user], record)),
            link_to('Destroy', user_business_path(options[:user], record), method: :delete, data: { confirm: 'Are you sure?' })
          ]
        end
      end
    
      def get_raw_records
        Business.where(user_id: options[:user].id)
      end
    end
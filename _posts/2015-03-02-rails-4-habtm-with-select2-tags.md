---
title: Rails 4 HABTM with Select2 Tags
author: Ygam Retuta
layout: post
permalink: /rails-4-habtm-with-select2-tags/
dsq_thread_id:
  - 3561844159
categories:
  - Uncategorized
---
**VERSIONS:**

  * Rails 4.2, 
  * Select2 3.5
  * select2-rails 3.5

**ASSUMPTIONS:**

  * app with resources: Business, Listing, Item
  * using Slim templates
  * using HABTM

**Code starts Here:**

    # Gemfile
    
    gem 'select2-rails'
    
    # app/models/business.rb
    
    class Business < ActiveRecord::Base has_many :items, dependent: :destroy has_many :listings, dependent: :destroy end
    

details for the following code can be found at: <https://coderwall.com/p/d8kcsw/many-to-many-in-rails-tagging-with-select2>

    # app/models/item.rb
    class Item < ActiveRecord::Base
      has_and_belongs_to_many :listings
      attr_reader :listing_ids
      attr_accessor :listing_tokens
    
      # https://coderwall.com/p/d8kcsw/many-to-many-in-rails-tagging-with-select2
      def listing_tokens=(tokens)
        self.listing_ids = Listing.ids_from_tokens(tokens)
      end
    end
    
    # app/models/listing.rb
    class Listing < ActiveRecord::Base
      belongs_to :business
      has_and_belongs_to_many :items
    
      validates :business_id, presence: true
      validates :name,
                presence: true,
                uniqueness: {scope: :business}
    
      delegate :user, to: :business
    
      # https://coderwall.com/p/d8kcsw/many-to-many-in-rails-tagging-with-select2
      def self.ids_from_tokens(tokens)
        tokens.gsub!(/<<<(.+?)>>>/) { create!(name: $1.capitalize).id }
        tokens.split(',')
      end
    
      scope :like_name, -> (name) {where('name LIKE ?', "#{name}%")}
    end
    

here are the controllers; only significant code is present

    # app/controllers/businesses_controller.rb
    class BusinessesController < ApplicationController
      before_action :set_business, only: [:edit, :update, :destroy, :listings]
    
      def listings
        @listings = @business.listings.like_name params[:name]
    
        respond_to do |format|
          format.json { render json: @listings }
        end
      end
    
      private
        # Use callbacks to share common setup or constraints between actions.
        def set_business
          @business = Business.friendly.find(params[:id])
        end
    
        # Never trust parameters from the scary internet, only allow the white list through.
        def business_params
          params.require(:business).permit(:name)
        end
    end
    
    # app/controllers/items_controller.rb
    class ItemsController < ApplicationController
      before_action :set_parents, except: [:remove_listing, :listings]
      before_action :set_item, only: [:show, :edit, :update, :destroy]
    
      def listings
        @item = Item.friendly.find params[:item_id]
        @listings = @item.listings
    
        respond_to do |format|
          format.json { render json: @listings, status: 200}
        end
      end
    
      def remove_listing
        @item = Item.friendly.find params[:item_id]
        id = params[:listing_id]
        @item.listings.delete(Listing.find id)
    
        respond_to do |format|
          format.json { head :no_content }
        end
      end
    
      private
        # Use callbacks to share common setup or constraints between actions.
        def set_parents
          @business = Business.friendly.find(params[:business_id])
        end
    
        def set_item
          @item = Item.friendly.find(params[:id])
        end
    
        # Never trust parameters from the scary internet, only allow the white list through.
        def item_params
          params.require(:item).permit(:name, :listing_id, :category_id, :price, :price_enabled,
                                       :listing_tokens, :available, pictures_array: [],
                                       pictures_attributes: [:id, :image, :_destroy])
        end
    end
    

here is the Item form:

    = form_for([@business, @item]) do |f|
      - if @item.errors.any?
        #error_explanation
          h2
            = pluralize(@item.errors.count, "error")
            |  prohibited this item from being saved:
          ul
            - @item.errors.full_messages.each do |message|
              li
                = message
      .field
        = f.label :name
        = f.text_field :name
    
      .field
        = f.label :listing_tokens, 'Listings'
    
        - if @item.id
          = f.text_field :listing_tokens,
                         data: { url: business_listings_path(@business),
                          listings_url: item_listings_path(@item),
                          remove_listings_url: item_remove_listings_path(@item)}
        - else
          = f.text_field :listing_tokens,
                         data: { url: user_business_listings_path(current_user, @business) }
    
      .submit-field
        = f.button 'Submit', data: { disable_with: 'Processing...' }
    

here is our main js file

    # app/javascripts/scripts.coffee
    format = (listing)-> 
        return listing.name
    
    $('#item_listing_tokens').select2(
        tags: true
        multiple: true
        minimumInputLength: 2
        width: '100%'
        maximumSelectionSize: 5
        formatSelectionTooBig: (limit) ->
          return 'You can only select five listings'
        ajax: {
          dataType: 'json'
          url: url
          data: (term) ->
            return { name: term }
          results: (data) ->
            console.log(data)
            return { results: data }
        }
        formatSelection: format
        formatResult: format
        data: []
        initSelection: (element, callback) ->
          data1 = []
    
          $.ajax
            type: 'get',
            url: listings_url
            dataType: 'json'
            async: false
            error: (xhr, status, error) ->
              console.log(error)
            success: (data)->
              $.each data, (i, obj)->
                data1.push
                  id: this.id
                  name: this.name
    
          callback(data1)
      ).select2('val', ['1', '2'])
    
      $('#item_listing_tokens').on 'change', (e)->
        if e.removed
          $.ajax
            type: 'POST'
            url: remove_listings_url
            dataType: 'json'
            data: { listing_id: e.removed.id }
            error: (xhr, status, message) ->
              alert(message)
    

  * Note the use of formatSelection and formatResult. We do this so we can use the Listing attribute `name`.
  * Note the use of another `select2` method. We do this to give an initial value to the input because for some reason selec2t tags won&#8217;t work without it.
  * Note the `initSelection` function. We use this to prepopulate our tags when we are using the edit action
  * Note the on change event handler. We use this to handle deletion of tags

**SPECIAL NOTE:**  
I haven&#8217;t added the functionality to create Listing tags on the fly. I don&#8217;t need it right now but if someday I happen to, I will add it here 😀
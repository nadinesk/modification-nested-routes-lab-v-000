# Modifying Nested Resources Lab

## Objectives

1. Implement nested resources for creation and modification

## Overview

In this lab, we're going to be implementing nested resources for
creating and editing songs through an artist.

## Instructions

1. Using nested resources, set up routes and controller actions to
   create new `song` records through an `artist`. **Hint:** Don't forget
to update the strong parameters.	
2. Set up routes and controller actions to support editing a `song` as a
   nested resource of an `artist`.

   routes.rb
   resources :artists do
    	resources :songs, only: [:index, :show, :new, edit]
  	  end

3. Create a helper to display a drop-down list of artists if someone
   edits a song directly via `/songs/id/edit` and to only display the
artist's name if they are editing through nested routing. Name the
helper method `artist_select`. **Hint:** You'll need to set a variable
in the controller action to pass to the helper method as an argument
along with a `song` instance.

	helpers/songs_helper.rb

	def artist_select(song)
    	if song.artist.nil?
      		select_tag "song[artist_id]", options_from_collection_for_select(Artist.all, :id, :name)
    	else
      		hidden_field_tag "song[artist_id]", song.artist_id
    	end
  	end


  	posts/_form.html.erb  	
	<%= form_for @song  do |f| %>
  		<%= f.label :title %>
  		<%= f.text_field :title %><br>
  		<%= artist_select(@post) %>
  		
  		<%= f.submit %>
	<% end %>



4. Validate that new songs created for an artist via nested routing are
   created for valid artists, and redirect to `/artists` if not.

   posts_controller.rb

   def new
   	if params[:artist_id] && !Artist.exists?(params[:artist_id])
    	redirect_to artists_path, alert: "Artist not found."
  	else
    	@song = Song.new(artist_id: params[:artist_id])
  	end
   end




5. Validate that songs being edited via nested routing have a valid artist. Redirect to `/artists` if not.
6. Validate that songs being edited via nested routing are in the
   artist's `songs` collection. Redirect to `/artists/id/songs` if not.

   def edit
   	if params[:artist_id]
    	artist = Artist.find_by(id: params[:artist_id])
    	if artist.nil?
      		redirect_to artists_path, alert: "Artist not found."
    	else
      		@song = artists.songs.find_by(id: params[:id])
      		redirect_to artist_posts_path(artist), alert: "Song not found." if @song.nil?
    	end
  	else
    	@song = Song.find(params[:id])
  	end
  end

7. Make sure all tests pass!

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/diy-nested-resources-lab' title='Modifying Nested Resources Lab'>Modifying Nested Resources Lab</a> on Learn.co and start learning to code for free.</p>

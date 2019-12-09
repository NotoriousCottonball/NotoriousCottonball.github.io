---
layout: post
title:      "Keto Calc: Dynamic REACT/RAILS API Build with Facebook Oath/User Login Flow"
date:       2019-12-09 17:36:54 -0500
permalink:  keto_calc_dynamic_react_rails_api_build_with_facebook_oath_user_login_flow
---


For my fifth Flatiron School project, I built an app using REACT front-end and RAILS API back-end. The application allows the user to calculate, log, and track Net Carbs while following a Ketogenic Diet. It features a Client-API Login Flow with Facebook Oath and User Authentication via http(s)-only session cookies with same-origin policy. Nutrition Data is provided by Nutritionix via their API. Users can store their daily entries for Net Carbs and Calories (plus Protein/Fat) via a database search or manual entry. Users may also enter their daily Weight so they can chart their progress against the metrics of Ketosis.

The most difficult part of the application was coding a custom user login flow allowing for Facebook Authenticaton. 

The basic idea is to initialize the facebook sdk and launch the facebook login button. 

Once the Welcome component mounts, we listen for a facebook status change by subscribing to that event:



```
facebookAuthorization = () => {
      let self = this;
      let scriptTag = document.createElement('script');
      scriptTag.type = 'text/javascript';
      scriptTag.src = process.env.REACT_APP_FACEBOOK_AUTH_PATH;
      scriptTag.addEventListener('load', e => {
    




        window.FB.Event.subscribe('auth.statusChange', self.onSuccess);
      });

      document.body.appendChild(scriptTag);

    }

    onSuccess = (response) => {
          if (response.status === 'connected') {
            console.log(response.authResponse)
            const { accessToken, userID } = response.authResponse;
            this.props.facebookLogin(accessToken, userID, this.props.history);
            console.log("Connected to Facebook");
          } else {
            console.log("Not Connected to Facebook")
          }
        }
```


We then dispatch an action which sends the credentials in the facebook authorization over to the backend Rails API:


```
export const facebookLogin = (accessToken, userID, history) => {
  const data = {
    token: accessToken,
    uid: userID
  }
  return dispatch => {
    return fetch("/api/v1/auth/facebook_login", {
      method: 'POST',
      headers: headers,
      body: JSON.stringify(data)
    }).then(r => r.json())
    .then(response => {
      if (response.error) {
        alert(response.error)
      } else {
        dispatch(setCurrentUser(response.data))
        dispatch(getDays())
        //dispatch(otherAction())
        history.push('/')
      }
    })
  }
}
```


And, finally, complete the handshake by validating the information received on the back-end via a Faraday request to facebook graph via the API:
```
def authenticate_facebook_user

     token = params[:token]

     response = Faraday.get("https://graph.facebook.com/app?access_token=#{token}")
     data = JSON.parse(response.body)

     if data['id'] == ENV['FACEBOOK_KEY']

        uid = params[:uid]

        response = Faraday.get("https://graph.facebook.com/#{uid}?access_token=#{token}&fields=email, name ")
        authorization = JSON.parse(response.body)

        @user = User.find_or_create_from_facebook(authorization)
        session[:user_id] = @user.id
        render json: UserSerializer.new(@user), status: :ok
     else
        render json: {
        error: "Invalid Credentials!"
        }
     end

  end
```



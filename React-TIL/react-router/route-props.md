# route props

## match

match 객체는 `<Route path>`이 어떻게 URL에 매칭되는지에 관한 정보를 가지고 있다. 해당 객체는 아래와 같은 property를 가지고 있다.

- params - (object) Key/value pairs parsed from the URL corresponding to the dynamic segments of the path  
- isExact - (boolean) true if the entire URL was matched (no trailing characters)  
- path - (string) The path pattern used to match. Useful for building nested <Route>s  
- url - (string) The matched portion of the URL. Useful for building nested <Link>s  

**match 객체를 접근하는 방법.**  

- Route component에서 this.props.match  
Route component as this.props.match
- Route render as ({ match }) => ()
- Route children as ({ match }) => ()
- withRouter as this.props.match
- matchPath as the return value
- useRouteMatch as the return value

If a Route does not have a path, and therefore always matches, you’ll get the closest parent match. Same goes for withRouter.

## loaction 

## history

## Present modally里的presentingViewController问题

- The view controller that calls the `presentViewController:animated:completion:` method **MAY NOT** be the one that actually performs the modal presentation. The presentation style determines how that view controller is to be presented, including the characteristics required of the presenting view controller. For example, a full-screen presentation must be initiated by a full-screen view controller. **If the current presenting view controller is not suitable, UIKit walks the view controller hierarchy until it finds one that is.** Upon completion of a modal presentation, UIKit updates the `presentingViewController` and `presentedViewController` properties of the affected view controllers.

- `UIModalTransitionStylePartialCurl`需要presentation style是`Full Screen`

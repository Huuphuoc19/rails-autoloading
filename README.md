# RAILS CONSTANTS AND AUTOLOADING

In a rails application

1. We have model `PointTransaction` inside module `Ps`
```ruby
  module Ps
    class PointTransaction
      ...
    end
  end
```

2. We have an api controller `Api::V1::Ps::DailyLoginsController`
```ruby
class Api::V1::Ps::DailyLoginsController
  # I1
  def index
    @txs = Ps::PointTransaction.all
  end

  # I2
  def index
    @txs = ::Ps::PointTransaction.all
  end
end
```

* I1
  * Parent namepsace as the algorithm [here](https://guides.rubyonrails.org/autoloading_and_reloading_constants.html#parent-namespaces), we have

    * `Api::V1::Ps::DailyLoginsController`
    * `Api::V1::Ps`
    * `Api::V1`
    * `Api`
    * `top namespace`

  * Resolve `Ps::PointTransaction` processes by [this procedure](https://guides.rubyonrails.org/autoloading_and_reloading_constants.html#generic-procedure) follows the above parent namespaces in order:
    * First run `Api::V1::Ps::DailyLoginsController`<br>
      Cannot found `api/v1/ps/daily_logins_controller/ps.rb`<br>
      Cannot found `api/v1/ps/daily_logins_controller/ps`<br>
      `Ps` is existed on the parent `Api::V1::Ps` => raise NameError`<br>
      Following [the logic](https://guides.rubyonrails.org/autoloading_and_reloading_constants.html#autoloading-algorithms-qualified-references)

      > If none of the parent namespaces of the class or module has the missing constant then Rails assumes the reference is relative. Otherwise qualified.

      That above logics force `Ps` must be defined on direct parent namespace `Api::V1::Ps::DailyLoginsController` => Finally the NameError is: `uninitialized constant Api::V1::Ps::DailyLoginsController::Ps`


* I2
  * `::Ps::PointTransaction.all` will force `Ps` was found on `top namesapce` => It'll run fine

#### Bonus `Api::V1::Ps::DailyLoginsController` was defined (also as [the procedure](https://guides.rubyonrails.org/autoloading_and_reloading_constants.html#generic-procedure))
  1. Find `Api`
  2. Find `Api::V1`
  3. Find `Api::V1::Ps`
  4. Find `Api::V1::Ps::DailyLoginsController`



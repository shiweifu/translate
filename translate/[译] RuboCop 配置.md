翻译自：[Rubocop Configuration Files for Rails - DEV Community](https://dev.to/truemark/rubocop-configuration-files-for-rails-39g)

> 我花了一整天时间在一个Rails项目中配置Rubocop。我是来帮你节约时间的。



Rubocop是一个用于Ruby和Rails项目的测试工具。它根据社区Ruby风格指南中列出的指导方针实施最佳实践。除了报告在代码中发现的问题外，RuboCop还可以自动为您修复其中的许多问题。



#### 针对 Rails 的配置



这个项目假设你已经在你的项目中设置了rubocop，并且有`rubocop.yml`。



##### 步骤1:在.rubocop.yml中添加规则



```
# The behavior of RuboCop can be controlled via the .rubocop.yml
# configuration file. It makes it possible to enable/disable
# certain cops (checks) and to alter their behavior if they accept
# any parameters. The file can be placed either in your home
# directory or in some project directory.
#
# RuboCop will start looking for the configuration file in the directory
# where the inspected file is and continue its way up to the root directory.
#

inherit_from:
  - '.rubocop-rails.yml'
  - '.rubocop-rspec.yml'

require:
  - rubocop-rails
  - rubocop-rspec

AllCops:
  TargetRubyVersion: 2.7
  TargetRailsVersion: 6.0
  Exclude:
    - '**/db/migrate/*'
    - '**/Gemfile.lock'
    - '**/Rakefile'
    - '**/rails'
    - '**/vendor/**/*'
    - '**/spec_helper.rb'
    - 'node_modules/**/*'
    - 'bin/*'

###########################################################
###################### Rubocop ############################
###########################################################

# You can find all configuration options for rubocop here: https://docs.rubocop.org/rubocop/cops_bundler.html

# ============== Layout =================

Layout/ClassStructure:
  ExpectedOrder:
    - module_inclusion
    - constants
    - association
    - public_attribute_macros
    - public_delegate
    - macros
    - initializer
    - public_class_methods
    - public_methods
    - protected_attribute_macros
    - protected_methods
    - private_attribute_macros
    - private_delegate
    - private_methods

Layout/EmptyLineAfterMultilineCondition:
  Enabled: true

Layout/EmptyLinesAroundAttributeAccessor:
  Enabled: true

Layout/FirstArrayElementIndentation:
  EnforcedStyle: consistent

Layout/FirstArrayElementLineBreak:
  Enabled: true

Layout/FirstHashElementIndentation:
  EnforcedStyle: consistent

Layout/FirstHashElementLineBreak:
  Enabled: true

Layout/LineLength:
  Max: 150
  Exclude:
    - '**/spec/**/*'

Layout/MultilineArrayBraceLayout:
  EnforcedStyle: new_line

Layout/MultilineOperationIndentation:
  EnforcedStyle: indented

Layout/MultilineHashBraceLayout:
  EnforcedStyle: new_line

Layout/MultilineHashKeyLineBreaks:
  Enabled: true

Layout/MultilineMethodCallBraceLayout:
  EnforcedStyle: new_line

Layout/MultilineMethodDefinitionBraceLayout:
  EnforcedStyle: new_line

Layout/SpaceAroundMethodCallOperator:
  Enabled: true

Layout/SpaceInLambdaLiteral:
  EnforcedStyle: require_space

Lint/AmbiguousBlockAssociation:
  Exclude:
    - '**/spec/**/*'

Lint/AssignmentInCondition:
  AllowSafeAssignment: false

Lint/BinaryOperatorWithIdenticalOperands:
  Enabled: true

Lint/DeprecatedOpenSSLConstant:
  Enabled: true

Lint/DuplicateElsifCondition:
  Enabled: true

Lint/DuplicateRequire:
  Enabled: true

Lint/DuplicateRescueException:
  Enabled: true

Lint/EmptyConditionalBody:
  Enabled: true

Lint/EmptyFile:
  Enabled: true

Lint/FloatComparison:
  Enabled: true

Lint/MissingSuper:
  Enabled: true

Lint/MixedRegexpCaptureTypes:
  Enabled: true

Lint/NumberConversion:
  Enabled: true

Lint/RaiseException:
  Enabled: true

Lint/SelfAssignment:
  Enabled: true

Lint/TrailingCommaInAttributeDeclaration:
  Enabled: true

Lint/UnusedBlockArgument:
  IgnoreEmptyBlocks: false

Lint/UnusedMethodArgument:
  IgnoreEmptyMethods: false

Lint/UselessMethodDefinition:
  Enabled: true

# ============== Metric =================

Metrics/AbcSize:
 Max: 45

Metrics/BlockLength:
  CountComments: false
  Max: 50
  Exclude:
    - '**/spec/**/*'
    - '**/*.rake'
    - '**/factories/**/*'
    - '**/config/routes.rb'

Metrics/ClassLength:
  CountAsOne: ['array', 'hash']
  Max: 150

Metrics/CyclomaticComplexity:
  Max: 10

Metrics/MethodLength:
  CountAsOne: ['array', 'hash']
  Max: 30

Metrics/ModuleLength:
  CountAsOne: ['array', 'hash']
  Max: 250
  Exclude:
    - '**/spec/**/*'

Metrics/PerceivedComplexity:
  Max: 10

# ============== Variable ==================

# Most of the Naming configurations are enabled by default, we should enable or disable configuration depending on what the team needs

### Example
##
#  Naming/VariableNumber:
#    Enabled: false
##
###

# ============== Style ================

Style/AccessorGrouping:
  Enabled: true

Style/ArrayCoercion:
  Enabled: true

Style/AutoResourceCleanup:
  Enabled: true

Style/BisectedAttrAccessor:
  Enabled: true

Style/CaseLikeIf:
  Enabled: true

Style/ClassAndModuleChildren:
  Enabled: false

Style/CollectionMethods:
  Enabled: true

Style/CombinableLoops:
  Enabled: true

Style/CommandLiteral:
  EnforcedStyle: percent_x

Style/ConstantVisibility:
  Enabled: true

Style/Documentation:
  Enabled: false

Style/ExplicitBlockArgument:
  Enabled: true

Style/GlobalStdStream:
  Enabled: true

Style/HashEachMethods:
  Enabled: true

Style/HashLikeCase:
  Enabled: true

Style/HashTransformKeys:
  Enabled: true

Style/HashTransformValues:
  Enabled: true

Style/ImplicitRuntimeError:
  Enabled: true

Style/InlineComment:
  Enabled: true

Style/IpAddresses:
  Enabled: true

Style/KeywordParametersOrder:
  Enabled: true

Style/MethodCallWithArgsParentheses:
  Enabled: true

Style/MissingElse:
  Enabled: true

Style/MultilineMethodSignature:
  Enabled: true

Style/OptionalBooleanParameter:
  Enabled: true

Style/RedundantAssignment:
  Enabled: true

Style/RedundantBegin:
  Enabled: true

Style/RedundantFetchBlock:
  Enabled: true

Style/RedundantFileExtensionInRequire:
  Enabled: true

Style/RedundantSelfAssignment:
  Enabled: true

Style/SingleArgumentDig:
  Enabled: true

Style/StringConcatenation:
  Enabled: true
```



### 第二步，创建 `.rubocop-rails.yml`



上面的配置文件，是针对 Ruby 语言的最佳实践，现在，我们针对 Rails 添加一些特殊的配置。



创建`.rubocop-rails.yml`(如果还没有创建的话)，并在其中添加以下内容：



```
###########################################################
#################### Rubocop Rails ########################
###########################################################

# You can find all configuration options for rubocop-rails here: https://docs.rubocop.org/rubocop-rails/cops_rails.html

Rails/ActiveRecordCallbacksOrder:
  Enabled: true

Rails/AfterCommitOverride:
  Enabled: true

Rails/DefaultScope:
  Enabled: true

Rails/FindById:
  Enabled: true

Rails/Inquiry:
  Enabled: true

Rails/MailerName:
  Enabled: true

Rails/MatchRoute:
  Enabled: true

Rails/NegateInclude:
  Enabled: true

Rails/OrderById:
  Enabled: true

Rails/Pluck:
  Enabled: true

Rails/PluckId:
  Enabled: true

Rails/PluckInWhere:
  Enabled: true

Rails/RenderInline:
  Enabled: true

Rails/RenderPlainText:
  Enabled: true

Rails/SaveBang:
  Enabled: true
  AllowImplicitReturn: false

Rails/ShortI18n:
  Enabled: true

Rails/WhereExists:
  Enabled: true

Rails/WhereNot:
  Enabled: true
```



#### 额外：针对 RSpec 的配置



提示：确保你已经安装  [rubocop-rspec](https://github.com/rubocop/rubocop-rspec)  这个 gem。



在项目的根目录，创建 `.rubocop-rspec.yml` 文件，添加以下内容：



```
###########################################################
#################### Rubocop Rspec ########################
###########################################################

# You can find all configuration options for rubocop-rspec here: https://docs.rubocop.org/rubocop-rspec/cops.html

RSpec/AnyInstance:
  Enabled: false

RSpec/BeforeAfterAll:
  Enabled: false

RSpec/ContextWording:
  Enabled: false

RSpec/DescribeClass:
  Enabled: false

RSpec/ExampleLength:
  Enabled: false

RSpec/ExpectInHook:
  Enabled: false

RSpec/FilePath:
  Enabled: false

RSpec/InstanceVariable:
  Enabled: false

RSpec/LetSetup:
  Enabled: false

RSpec/MessageChain:
  Enabled: false

RSpec/MessageSpies:
  Enabled: false

RSpec/MultipleExpectations:
  Enabled: false

RSpec/NamedSubject:
  Enabled: false

RSpec/NestedGroups:
  Max: 7

RSpec/SubjectStub:
  Enabled: false

RSpec/VerifiedDoubles:
  Enabled: false

RSpec/VoidExpect:
  Enabled: false
```


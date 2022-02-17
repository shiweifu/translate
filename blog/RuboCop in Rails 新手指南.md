RuboCop 是一个静态代码分析器，基于 Ruby 社区关于代码风格的最佳实践：[Ruby style guide](https://rubystyle.guide/)。



除了代码分析功能之外，它还提供了自动格式化代码，以及修复代码中出现的警告两个特性。



如果你有 JavaScript 开发背景，你也许听说过 ESLint。



> RuboCop 相当于 Ruby 版的 ESLint。



Ruby 以外，RuboCop 还提供各种扩展 Gem，如对 Rails，Minitest，RSPect 等的支持。



#### 为什么使用 RuboCop？



它引发了我们的疑问和好奇，为什么我们需要使用 RuboCop 呢？在我们的项目中使用 RuboCop 有什么用呢？



1. 整洁的代码



我们都希望可以编写整洁的代码，并遵循世界各地开发人员遵循的关于代码的最佳实践。最佳实践来自开发经验，如果我们只依靠自己摸索，可能需要花几年时间了解所使用的语言，了解开发中的反模式和好的模式。



通过使用 RuboCop，我们不再需要非常多的经验。因为最佳实践以规则的形式，已经内置到 `rubocop` gem 啦。当我们的代码不符合这些规则时，RuboCop 会发出警告。根据警告修复这些代码中的问题之后，我们的代码往往会变得整洁和易于理解。



2. 简化代码审查过程



代码审查的主要目的是修复代码中逻辑问题、修复安全问题，或者讨论我们所开发功能的路径。



但是，想象一下这样一种情况：我们推送了一个有拼写错误的代码，评审者发现了它，然后对它进行评论以进行修复，因为显然没有人愿意将有拼写错误的代码交付到生产环境中。



这有什么问题吗？审查代码需要花费大量时间用于审查对于拼写错误或者讨论关于合并请求的最佳实践，我们作为开发人员，浪费了大量的时间。这些请将，可以通过配置 RuboCop 的规则，来进行解决。



RuboCop 可以保证这些类似的代码问题，不会出现在合并或者 PR 中。



3. 最佳实践并不是万能的



通常情况下，最佳实践意味着我们在编写代码时，我们所遵循的，s无论喜欢或者不喜欢，关于代码或者模式的规则，对于每个人都不一样。如果我们把精力聚焦在讨论每个功能中，关于代码的实践，那么我们什么时候能发布这些功能呢？



而使用 Rubocop，我们可以与团队成员讨论应该遵循哪些规则，或者禁用哪些规则，从而让团队中的每个人都满意（好吧，你永远不可能让每个人都满意）。



#### 在 Rails，设置 RuboCop 



在本文中，我们安装 `rubocop` gem，来指定对 Ruby 代码的规则约束，通过 `rubocop-rails`，来指定 Rails 代码的约束。



#### 添加 Gem 到 Gemfile



添加以下依赖，到 `:development，:test`：



```
group :development, :test do
  # enforce rails best practice with rubocop
  gem 'rubocop', '~> 1.18.0', require: false
  gem 'rubocop-performance', '~> 1.11.0', require: false
  gem 'rubocop-rails', '~> 2.11.0', require: false
end
```



> 注意：在我们的项目中安装这些 gem 时，指定当前最新版本的 gem。



我们添加了下列 gem，到 Gemfile 中：



- rubocop ：Ruby 代码规则
- rubocop-performance：关于性能方面的规则
- rubocop-rails：专用于 Rails 的规则



#### 在项目中安装 Gem



1. 全局安装 `rubocop`

`gem install rubocop`



这将帮助我们运行 `rubocop` gem 包，所提供的命令，如自动格式化代码、在项目中运行 rubocop 等。



2. 通过 `bundle install` 安装项目所需要的 gem 包



#### 增加配置文件



要控制规则（激活、禁用），我们需要为每个扩展创建配置文件。如果没有配置文件，RuboCop 将激活默认扩展配置。我喜欢创建相应的配置文件，因为它为我们的项目提供灵活性。



让我们来为 RuboCop 相关的扩展来创建配置：



```
  $ cd /path/to/our/project
  $ touch .rubocop.yml
  $ touch .rubocop-performance.yml
  $ touch .rubocop-rails.yml
```



#### 向配置文件添加规则



>  我也有一个专门介绍 RuboCop 配置文件写的日志，如果你想要更多的选项，你可以在 [RuboCop Configuration Files for Rails](https://prabinpoudel.com.np/articles/rubocop-configuration-files-for-rails/) 找到它。



让我们更新配置文件，添加 Ruby  相关的规则，并安装扩展。



#### Ruby



```
# .rubocop.yml

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
  - '.rubocop-performance.yml'
  - '.rubocop-rails.yml'

require:
  - rubocop-performance
  - rubocop-rails

AllCops:
  TargetRubyVersion: 2.7
  TargetRailsVersion: 6.0
  Exclude:
    - '**/db/migrate/*'
    - 'db/schema.rb'
    - '**/Gemfile.lock'
    - '**/Rakefile'
    - '**/rails'
    - '**/vendor/**/*'
    - '**/spec_helper.rb'
    - 'node_modules/**/*'
    - 'bin/*'

###########################################################
###################### RuboCop ############################
###########################################################

# You can find all configuration options for rubocop here: https://docs.rubocop.org/rubocop/cops_bundler.html

###########################################################
####################### Gemspec ###########################
###########################################################

Gemspec/DateAssignment: # (new in 1.10)
  Enabled: true

###########################################################
######################## Layout ###########################
###########################################################

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

Layout/LineEndStringConcatenationIndentation: # (new in 1.18)
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

Layout/SpaceBeforeBrackets: # (new in 1.7)
  Enabled: true

Layout/SpaceInLambdaLiteral:
  EnforcedStyle: require_space


###########################################################
######################## Lint #############################
###########################################################

Lint/AmbiguousAssignment: # (new in 1.7)
  Enabled: true

Lint/AmbiguousBlockAssociation:
  Exclude:
    - '**/spec/**/*'

Lint/AssignmentInCondition:
  AllowSafeAssignment: false

Lint/BinaryOperatorWithIdenticalOperands:
  Enabled: true

Lint/DeprecatedConstants: # (new in 1.8)
  Enabled: true

Lint/DeprecatedOpenSSLConstant:
  Enabled: true

Lint/DuplicateBranch: # (new in 1.3)
  Enabled: true

Lint/DuplicateElsifCondition:
  Enabled: true

Lint/DuplicateRegexpCharacterClassElement: # (new in 1.1)
  Enabled: true

Lint/DuplicateRequire:
  Enabled: true

Lint/DuplicateRescueException:
  Enabled: true

Lint/EmptyBlock: # (new in 1.1)
  Enabled: true

Lint/EmptyClass: # (new in 1.3)
  Enabled: true

Lint/EmptyConditionalBody:
  Enabled: true

Lint/EmptyFile:
  Enabled: true

Lint/EmptyInPattern: # (new in 1.16)
  Enabled: true

Lint/FloatComparison:
  Enabled: true

Lint/LambdaWithoutLiteralBlock: # (new in 1.8)
  Enabled: true

Lint/MissingSuper:
  Enabled: true

Lint/MixedRegexpCaptureTypes:
  Enabled: true

Lint/NoReturnInBeginEndBlocks: # (new in 1.2)
  Enabled: true

Lint/NumberConversion:
  Enabled: true

Lint/NumberedParameterAssignment: # (new in 1.9)
  Enabled: true

Lint/OrAssignmentToConstant: # (new in 1.9)
  Enabled: true

Lint/RaiseException:
  Enabled: true

Lint/RedundantDirGlobSort: # (new in 1.8)
  Enabled: true

Lint/SelfAssignment:
  Enabled: true

Lint/SymbolConversion: # (new in 1.9)
  Enabled: true

Lint/ToEnumArguments: # (new in 1.1)
  Enabled: true

Lint/TrailingCommaInAttributeDeclaration:
  Enabled: true

Lint/TripleQuotes: # (new in 1.9)
  Enabled: true

Lint/UnexpectedBlockArity: # (new in 1.5)
  Enabled: true

Lint/UnmodifiedReduceAccumulator: # (new in 1.1)
  Enabled: true

Lint/UnusedBlockArgument:
  IgnoreEmptyBlocks: false

Lint/UnusedMethodArgument:
  IgnoreEmptyMethods: false

Lint/UselessMethodDefinition:
  Enabled: true

###########################################################
######################## Metric ###########################
###########################################################

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

###########################################################
######################## Naming ###########################
###########################################################

Naming/InclusiveLanguage: # (new in 1.18)
  Enabled: true

###########################################################
######################## Style ############################
###########################################################

Style/AccessorGrouping:
  Enabled: true

Style/ArgumentsForwarding: # (new in 1.1)
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

Style/CollectionCompact: # (new in 1.2)
  Enabled: true

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

Style/DocumentDynamicEvalDefinition: # (new in 1.1)
  Enabled: true

Style/EndlessMethod: # (new in 1.8)
  Enabled: true

Style/ExplicitBlockArgument:
  Enabled: true

Style/GlobalStdStream:
  Enabled: true

Style/HashConversion: # (new in 1.10)
  Enabled: true

Style/HashEachMethods:
  Enabled: true

Style/HashExcept: # (new in 1.7)
  Enabled: true

Style/HashLikeCase:
  Enabled: true

Style/HashTransformKeys:
  Enabled: true

Style/HashTransformValues:
  Enabled: true

Style/IfWithBooleanLiteralBranches: # (new in 1.9)
  Enabled: true

Style/ImplicitRuntimeError:
  Enabled: true

Style/InlineComment:
  Enabled: true

Style/InPatternThen: # (new in 1.16)
  Enabled: true

Style/IpAddresses:
  Enabled: true

Style/KeywordParametersOrder:
  Enabled: true

Style/MethodCallWithArgsParentheses:
  Enabled: true

Style/MissingElse:
  Enabled: true

Style/MultilineInPatternThen: # (new in 1.16)
  Enabled: true

Style/MultilineMethodSignature:
  Enabled: true

Style/NegatedIfElseCondition: # (new in 1.2)
  Enabled: true

Style/NilLambda: # (new in 1.3)
  Enabled: true

Style/OptionalBooleanParameter:
  Enabled: true

Style/QuotedSymbols: # (new in 1.16)
  Enabled: true

Style/RedundantArgument: # (new in 1.4)
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

Style/StringChars: # (new in 1.12)
  Enabled: true

Style/StringConcatenation:
  Enabled: true

Style/SwapValues: # (new in 1.1)
  Enabled: true
```



#### Rails



```
# .rubocop-rails.yml

###########################################################
#################### RuboCop Rails ########################
###########################################################

# You can find all configuration options for rubocop-rails here: https://docs.rubocop.org/rubocop-rails/cops_rails.html

Rails/ActiveRecordCallbacksOrder:
  Enabled: true

Rails/AddColumnIndex: # (new in 2.11)
  Enabled: true

Rails/AfterCommitOverride:
  Enabled: true

Rails/AttributeDefaultBlockValue: # (new in 2.9)
  Enabled: true

Rails/DefaultScope:
  Enabled: true

Rails/EagerEvaluationLogMessage: # (new in 2.11)
  Enabled: true

Rails/ExpandedDateRange: # (new in 2.11)
  Enabled: true

Rails/FindById:
  Enabled: true

Rails/I18nLocaleAssignment: # (new in 2.11)
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

Rails/SquishedSQLHeredocs: # (new in 2.8)
  Enabled: true

Rails/TimeZoneAssignment: # (new in 2.10)
  Enabled: true

Rails/UnusedIgnoredColumns: # (new in 2.11)
  Enabled: true

Rails/WhereEquals: # (new in 2.9)
  Enabled: true

Rails/WhereExists:
  Enabled: true

Rails/WhereNot:
  Enabled: true
```



#### 性能优化



```
.rubocop-performance.yml

###########################################################
#################### RuboCop Performance ##################
###########################################################

# You can find all configuration options for rubocop-performance here: https://docs.rubocop.org/rubocop-performance/

Performance/AncestorsInclude: # (new in 1.7)
  Enabled: true

Performance/BigDecimalWithNumericArgument: # (new in 1.7)
  Enabled: true

Performance/BlockGivenWithExplicitBlock: # (new in 1.9)
  Enabled: true

Performance/CollectionLiteralInLoop: # (new in 1.8)
  Enabled: true

Performance/ConstantRegexp: # (new in 1.9)
  Enabled: true

Performance/MapCompact: # (new in 1.11)
  Enabled: true

Performance/MethodObjectAsBlock: # (new in 1.9)
  Enabled: true

Performance/RedundantEqualityComparisonBlock: # (new in 1.10)
  Enabled: true

Performance/RedundantSortBlock: # (new in 1.7)
  Enabled: true

Performance/RedundantSplitRegexpArgument: # (new in 1.10)
  Enabled: true

Performance/RedundantStringChars: # (new in 1.7)
  Enabled: true

Performance/ReverseFirst: # (new in 1.7)
  Enabled: true

Performance/SortReverse: # (new in 1.7)
  Enabled: true

Performance/Squeeze: # (new in 1.7)
  Enabled: true

Performance/StringInclude: # (new in 1.7)
  Enabled: true

Performance/Sum: # (new in 1.8)
  Enabled: true
```


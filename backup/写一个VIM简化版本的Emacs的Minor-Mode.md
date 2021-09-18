创建文件me-mode.el，放在.emacs.d里的elisp目录。

`(define-minor-mode me-local-mode
	"simplified vim minor mode"
	:init-value nil
	:global 1
	:lighter " me"
	:keymap me-local-mode-map)`

定义minor mode

`(defvar me-local-mode-map
	(make-keymap))`

定义keymap

`(defun me-mode-disable ()
	"Disable my evil mode on special occation"
	(interactive)
	(me-local-mode -1))`

定义disable函数

`(defun me-mode-enable ()
	"auto enable my evil mode when evil mode"
	(interactive)	
	(if (and (not (bound-and-true-p me-local-mode))
					 (/= (point) (line-beginning-position)))
			(backward-char))
	(me-local-mode 1))`

定义enable函数，当前行不为空时，回退一个字符

`(add-hook 'minibuffer-setup-hook 'me-mode-disable)
(add-hook 'minibuffer-exit-hook 'me-mode-enable)`

minibuffer对应处理

`(provide 'me-mode)`

在文件最后写上provide

`(require 'me-mode)
(global-set-key [escape] 'me-mode-enable)`

在init.el里require，并绑定ESC

`(defun me-dummy-bind ()
	(interactive)
	(message "dummy key"))`

定义函数，屏蔽所有不需要的按键

`(define-key me-local-mode-map (kbd "t") 'me-dummy-bind)
(define-key me-local-mode-map (kbd "T") 'me-dummy-bind)
(define-key me-local-mode-map (kbd "U") 'me-dummy-bind)
(define-key me-local-mode-map (kbd "X") 'me-dummy-bind)
(define-key me-local-mode-map (kbd "Y") 'me-dummy-bind)
(define-key me-local-mode-map (kbd "z") 'me-dummy-bind)
(define-key me-local-mode-map (kbd "Z") 'me-dummy-bind)
(define-key me-local-mode-map (kbd "DEL") 'me-dummy-bind)
(define-key me-local-mode-map (kbd "RET") 'me-dummy-bind)
(define-key me-local-mode-map (kbd "SPC") 'me-dummy-bind)`

所有不需要响应的按键，都屏蔽

`(defun me-move-beginning ()
	"move beginning and tab"
	(interactive)
	(move-beginning-of-line nil)
	(indent-for-tab-command))`

`(define-key me-local-mode-map (kbd "0") 'me-move-beginning)`

当按下0时移动到行首，并tab一下

`(define-key me-local-mode-map (kbd "<") 'beginning-of-buffer)
(define-key me-local-mode-map (kbd ">") 'end-of-buffer)
(define-key me-local-mode-map (kbd "$") 'move-end-of-line)`

< 转到文件头 ，> 转到文件尾，$ 转到行尾

`(define-key me-local-mode-map (kbd "u") 'undo)`

u 代替C-x u

`(define-key me-local-mode-map (kbd "e") 'move-end-of-line)
(define-key me-local-mode-map (kbd "h") 'backward-char)
(define-key me-local-mode-map (kbd "i") 'me-mode-disable)
(define-key me-local-mode-map (kbd "j") 'next-line)
(define-key me-local-mode-map (kbd "k") 'previous-line)
(define-key me-local-mode-map (kbd "l") 'forward-char)`

e 与 $ 一样，光标移至行尾，kjhl 上下左右，i 关闭 me-mode

`(defvar-local me-pre-keystrokes '()
	"record pre keystrokes to perform next operation")`

有一些需要多个按键，比如 dw, dd, cw等等，需要把暂时用不上的放入这个list

`(defvar-local me-line-selection-overlay nil
	"the overlay of just line selection")`

使用overlay技术，主要为大V命令用的

`(defvar-local me-visual-begin-pos nil
	"begin pos of the visual line")`

配合大V命令使用，标记行选取的开始，需要计算

`(defvar-local me-visual-end-pos nil
	"end pos of the visual line")`

配合大V命令使用，标记行选取的结束，需要计算

`(defun me-clear-keystrokes ()
	"clear saved keystrokes"
	(setq me-pre-keystrokes '()))`

多个按键触发了命令，需要清空保存的按键

`(defun me-advice-clear-everything ()
	(me-clear-keystrokes)
	;;(message "clear keystrokes")
	(if me-line-selection-overlay
			(delete-overlay me-line-selection-overlay))
	(setq me-line-selection-overlay nil)
	(setq me-visual-begin-pos nil)
	(setq me-visual-end-pos nil))`

清除所有自定义变量，保存的按键，大V用的overlay，起始结束位置

`(advice-add #'keyboard-quit :before #'me-advice-clear-everything)`

使用advice，加入C-g的后续函数列表，保证按下C-g，不污染任何后续操作

`(advice-add #'next-line :after #'me-advice-next-line)
(advice-add #'previous-line :after #'me-advice-previous-line)`

在大V按下的前提下，在上下行移动时，需要重新计算begin-pos和end-pos的位置

`(defun me-advice-next-line (&rest r)
	"move next line check visual selection"
	(if me-line-selection-overlay
			(progn
				(me-recalc-visual-pos-next-line)
				(move-overlay me-line-selection-overlay me-visual-begin-pos me-visual-end-pos)
				(overlay-put me-line-selection-overlay 'face 'region))))`

`(defun me-advice-previous-line (&rest r)
	"move previous line check visual selection"
	(if me-line-selection-overlay
			(progn
				(me-recalc-visual-pos-previous-line)
				(move-overlay me-line-selection-overlay me-visual-begin-pos me-visual-end-pos)
				(overlay-put me-line-selection-overlay 'face 'region))))`

在有大Voverlay的前提下，先计算一下位置，在重新标记overlay

`(defun me-recalc-visual-pos-next-line ()
	"only under visual line selection"
	;;(message "%s %s %s" (line-beginning-position) me-visual-begin-pos me-visual-end-pos)
	(if (and (> (line-beginning-position) me-visual-begin-pos)
					 (< (line-beginning-position) me-visual-end-pos))
			(setq me-visual-begin-pos (line-beginning-position))
		(setq me-visual-end-pos (line-end-position))))`

`(defun me-recalc-visual-pos-previous-line ()
	"only under visual line selection"
	(if (and (> (line-end-position) me-visual-begin-pos)
					 (< (line-end-position) me-visual-end-pos))
			(setq me-visual-end-pos (line-end-position))
		(setq me-visual-begin-pos (line-beginning-position))))`

重新计算位置，分开往上和往下两个函数分开调用

`(defun me-make-line-visual-selection ()
	"start line visual selection"
	(interactive)
	(me-advice-clear-everything)
	(me-refresh-visual-pos)
	(if me-line-selection-overlay
			(move-overlay me-line-selection-overlay me-visual-begin-pos me-visual-end-pos)
		(setq me-line-selection-overlay
					(make-overlay me-visual-begin-pos me-visual-end-pos)))
	(overlay-put me-line-selection-overlay 'face 'region))`

`(define-key me-local-mode-map (kbd "V") 'me-make-line-visual-selection)`

绑定到大V上，先清空所有自定义变量，在初始化一下起始和结束位置，之后构造overlay

`(defun me-refresh-visual-pos ()
	"update the var's value"
	(if me-visual-begin-pos
			(let ((line-begin-pos-now (line-beginning-position)))
				(setq me-visual-begin-pos (if (< line-begin-pos-now me-visual-begin-pos)
																	 line-begin-pos-now
																 me-visual-begin-pos)))
		(setq me-visual-begin-pos (line-beginning-position)))
	(if me-visual-end-pos
			(let ((line-end-pos-now (line-end-position)))
				(setq me-visual-end-pos (if (> line-end-pos-now me-visual-end-pos)
																	 line-end-pos-now
																 me-visual-end-pos)))
		(setq me-visual-end-pos (line-end-position))))`

计算overlay的起始和结束位置

`(define-key me-local-mode-map (kbd "d") 'me-d-bind)`

绑定d按键

`(defun me-d-bind ()
	"when press d"
	(interactive)
	(if (null (car me-pre-keystrokes)) ;; no pre keys
			(cond	(mark-active ;; by set-mark-command
						 (kill-region (region-beginning) (region-end)))
						(me-line-selection-overlay ;; by big V
						 (progn
							 (kill-region me-visual-begin-pos me-visual-end-pos)
							 (me-advice-clear-everything)))
						(t
						 (push 'd me-pre-keystrokes)))
		(if (eq 'd (car me-pre-keystrokes)) ;; double d
				(progn
					(me-trigger-dd)
					(me-advice-clear-everything)))))`

如果没有前置按键比如dd这样的，则清除标记区域或者大V标记的overlay区域，要不然就push 到 list中，以备后续使用

如果有前置d按键，那么就触发dd，删除当前行操作，再清除所有自定义变量

`(defun me-trigger-dd ()
	"check if can trigger dd operation"
	(let ((empty-line? (eq (line-beginning-position)
												 (line-end-position))))
		(ignore-errors
			(move-beginning-of-line nil)
			(kill-line))
		(if (not empty-line?)
				(delete-char 1))))`

删除当前行，如果还剩一个字符，把那个字符也删了

`(define-key me-local-mode-map (kbd "w") 'me-w-bind)`

绑定w按键

`(defun me-w-bind ()
	"when press w"
	(interactive)
	(cond ((null (car me-pre-keystrokes))
				 (forward-word))
				((eq 'd (car me-pre-keystrokes))
				 (me-trigger-dw)
				 (me-advice-clear-everything))
				((eq 'c (car me-pre-keystrokes))
				 (me-trigger-dw)
				 (me-advice-clear-everything)
				 (me-mode-disable))))`

如果没有前置按键，只是往前移动一个单词。

如果前置按键是d，那么触发dw，删除一个字符，并把所有变量清除

如果前置按键是c，那么触发cw，删除一个字符，清空所有两边，并disable掉me-mode

`(defun me-trigger-dw ()
	"kill one word"
	(ignore-errors
		(kill-region (point)
								 (progn
									 (forward-word)
									 (point)))))`

删除一个word，并忽略所有错误，防止影响后续函数调用

其他支持命令后续持续添加中，目前暂时够用

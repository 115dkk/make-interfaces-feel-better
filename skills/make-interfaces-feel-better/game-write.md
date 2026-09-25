# Writing: Games

Detailed policy for game text, under [write.md](write.md). Every rule in `write.md` still applies to a game's chrome: the store page, login, settings, real errors and the menus around the fiction. This file covers only what a game adds, which is a world with characters who can speak. The failures below come from generated games, and the fixes from rewrites the owner made.

## 사례: 터미널을 가르치는 '생활형 커리어 RPG'

터미널 명령을 가르치는 생성형 게임의 첫 화면 세 장입니다. 캐릭터 생성, 메일로 받은 첫 의뢰, 터미널 순서입니다.

**캐릭터 생성**

- 화면 맨 위의 "생활형 커리어 RPG"는 장르 이름입니다. 플레이어는 이미 게임을 켰으므로 장르를 알려 줄 필요가 없습니다. 장르는 스토어 설명에 씁니다.
- "작은 방, 오래된 컴퓨터 한 대. 오늘부터 컴퓨터로 돈을 버는 일을 시작합니다."는 분위기를 내려고 쓴 문장입니다. 이 문장을 읽어도 주인공이 누구이고 무슨 일을 하는지 알 수 없습니다.
- "캐릭터 만들기", "온라인 핸들", "사용할 수 있는 핸들입니다", "실명이 아니어도 됩니다"는 웹 서비스 회원가입 문구를 그대로 옮긴 것입니다. 핸들이 게임 안에서 무엇인지 아무도 모르니 "이름은 계약서와 업무 메일에, 핸들은 커뮤니티 활동에 사용됩니다"라는 해설을 따로 달아야 했습니다.
- 피부색 다섯 가지, 머리색 여섯 가지, 옷 색 여섯 가지, 머리 모양 세 가지는 게임 어디에서도 쓰이지 않습니다. RPG라면 외형 설정이 있어야 한다는 관습만 보고 넣었습니다.
- 왼쪽 요약 카드는 오른쪽 입력란의 값을 한 번 더 보여 줍니다. 같은 카드 안에서 '이름'은 한국어이고 'Handle'은 영어입니다.

**첫 의뢰 메일**

- 물류회사 전산지원팀이 처음 보는 외부인에게 업무용 컴퓨터의 원격 접속을 맡깁니다. 현실에서 이런 메일은 사기이고, 같은 받은편지함에 '의심' 표시가 붙은 "재택으로 하루 30만원" 메일과 구별할 근거도 없습니다. 파일 찾기 과제를 먼저 정하고 의뢰는 그 과제에 맞춰 지어냈기 때문입니다.
- 의뢰 내용, 예상 시간, 작업비를 본문 목록과 아래 카드에 두 번 적었습니다. "수락하시면 원격 작업용 터미널이 열립니다"는 버튼을 누르면 일어날 일을 미리 해설하는 문장입니다.

**터미널**

- "실행 환경 없음 · 실제 Bash 세션 · 없음", "연습용 컴퓨터에 연결할 수 없습니다", "운영자에게 실행 환경을 확인해 달라고 알려주세요"는 게임을 돌리는 서버의 사정입니다. 게임 속 인물에게는 운영자도 실행 환경도 없습니다. '연습용 컴퓨터'라는 말에서 이 세계가 연습 문제라는 것이 드러납니다.
- 같은 연결 오류를 검은 패널과 노란 배너에 두 번 적었고, '다시 연결' 버튼은 세 개입니다.
- "완료 흐름 미리보기"는 개발 중에 쓰던 디버그 기능의 이름입니다.
- 힌트 1부터 4까지는 정답을 네 번에 나눠 보여 줍니다. 힌트 4는 입력할 명령을 순서대로 적어 두었고, 마지막 줄 "힌트를 모두 확인했습니다"는 플레이어가 방금 한 일을 다시 알립니다.

이 화면들의 잘못은 두 가지입니다. 첫째, 가르칠 기능부터 정하고 세계는 그 핑계로 만들었습니다. `pwd`, `ls`, `cd`, `cat`을 가르쳐야 하니 파일 찾기 의뢰를 만들고, 돈을 붙이고, 메일과 은행과 상점과 평판을 붙인 다음 '생활형 커리어 RPG'라는 이름을 달았습니다. 그래서 메뉴마다 튜토리얼의 핑계로 보입니다. 둘째, 기계의 말과 사람의 말을 구분하지 않았습니다. 시스템 메시지는 서버 사정을 늘어놓는데, 주인공이 반응하는 대사는 한 줄도 없습니다.

고쳐 쓴 첫 문장은 "오늘 탐정 사무소를 차리며 컴퓨터라는 걸 새로 장만했는데, 정말 의뢰가 오는 건가?"입니다. 이 한 문장에 주인공이 탐정이라는 것, 오늘 개업했다는 것, 컴퓨터가 새로 산 도구라는 것, 아직 손님이 없다는 것이 다 들어 있습니다. 분위기를 따로 쓰지 않았는데 사실을 말하니 분위기가 생겼습니다.

## Build the Situation Before the Feature

Do not dress a feature up to look like a game. Decide first who the player character is and why that person needs the feature; the menus then follow from the job.

| Before (feature first) | After (situation first) |
| --- | --- |
| 작은 방, 오래된 컴퓨터 한 대. 오늘부터 컴퓨터로 돈을 버는 일을 시작합니다. | 오늘 탐정 사무소를 차리며 컴퓨터라는 걸 새로 장만했는데, 정말 의뢰가 오는 건가? |
| 생활형 커리어 RPG (genre label above the screen title) | (delete; the store page names the genre) |
| 업무용 컴퓨터를 원격으로 확인해 주실 수 있을까요? (a company handing remote access to a stranger) | 의뢰인이 오래된 컴퓨터를 하나 가져왔다. 계약서가 들어 있다는데 어디에 저장했는지 기억하지 못한다고 한다. |

- **Every menu needs a reason in the job.** For the detective: 메일 receives cases, 터미널 examines a client's disk, 브라우저 searches public records, 은행 holds fees and office costs, 상점 sells investigation tools, 평판 records solved and failed cases. A menu with no such reason is cut.
- **A beginner protagonist makes plain teaching text acceptable.** When the character has just bought their first computer, "pwd는 현재 위치를 확인하는 명령입니다." is the character learning a new tool, and the player is not being talked down to.
- **The premise must hold up inside the world.** If the first case would be a scam, a security breach or a legal problem in the world the game depicts, change the case. A client who brings the machine to the office needs no remote access at all.
- **Let the lesson turn into the plot.** Once `ls` and `cat` work, the next case can hinge on them: the contract's modified date does not match the client's story. The commands become the means of investigation.
- **State the situation and let the mood follow.** An opening that tells the player who they are, what happened today and what they are waiting for creates the mood by itself. A line written only for mood ("작은 방, 오래된 컴퓨터 한 대") tells the player nothing.

## Inputs Are Facts About the Character

A form inside the game asks for facts about the character in the character's world. A sign-up form in costume is still a sign-up form.

| Before | After |
| --- | --- |
| 캐릭터 만들기 / 이름 / 온라인 핸들 | 내 이름과 갤러리 식별코드가 뭐더라? |
| 이름은 계약서와 업무 메일에, 핸들은 커뮤니티 활동에 사용됩니다. | (delete; the question already says where the code is used) |
| 외형 (skin, hair, clothing swatches) | 의뢰인에게 보일 내 모습, or delete the section |

- **Cut options nothing in the game uses.** Six hair colours are not required because the genre is RPG. Keep appearance only if the game shows it somewhere that matters, and give it that reason on screen.
- **Validation stays short and factual.** "이미 누가 쓰는 식별코드입니다." is enough.

## Two Speakers: the Machine and the Character

The machine speaks like a machine and the people speak like people. System text (terminal output, boot screens, device errors) is dry and exact, in the machine's own register (합니다체 or -십시오, or the real English output of the real tool). The protagonist's reaction goes in a separate monologue line, may be casual and carries the motive.

| Speaker | Line |
| --- | --- |
| System | 부트로더를 찾을 수 없습니다. 디스크를 확인한 뒤 다시 시작하십시오. |
| Character | 아니, 이 컴퓨터 작동을 안 하잖아! 의뢰인에게 물어보기엔 창피한데. 방법이 없을까? |

The last sentence gives the player a goal (find out without asking) with no objective marker.

Failure lines carry character too, and escalate with the situation:

| Situation | System | Character |
| --- | --- | --- |
| Wrong command | `command not found` | 음. 적어도 이건 아닌가 보다. |
| Several failures in a row | (same) | 설명서를 좀 읽어볼까. |
| A destructive command | (asks for confirmation) | 잠깐. 이걸 실행해도 되는 건가? |

- **The program itself never has feelings.** "함께해요", "걱정하지 마세요", "다시 시작해볼까요?" are the interface pretending to care, and they stay banned (see [write.md](write.md)). A game has characters who really can feel something; give the emotion to them.
- **Keep the two visually apart.** Different region, typeface or frame for system text and monologue, so the player never wonders whether a line is the machine's state or the character's opinion.
- **This is the character-voice exception in [write.md](write.md#korean-register).** Monologue and dialogue may use 해요체 or 반말; system text and everything outside the fiction do not.

## Keep the Real World Out of the Fiction

Inside the fiction, nothing may mention the game's own infrastructure: no 운영자, no 실행 환경, no 서버, no 연습용 or 튜토리얼 in a character's world.

- **Tell apart a failure the story contains from one the software has.** A broken bootloader on the client's PC is part of the case and is written in the world, as above. A sandbox that did not start is a real failure: show it once, outside the fiction layer (an out-of-game notice, not the in-world terminal), as a plain `write.md` error with the cause and the next action.
- **Never disguise a real outage as a story event.** A player who thinks the dead terminal is a puzzle will waste an hour on it.
- **Say each failure once.** One notice, one retry control.
- **Debug and preview tools do not ship.** "완료 흐름 미리보기" belongs behind a developer flag.

## Clues in the World, Not Hint Ladders

A numbered hint ladder (힌트 1 개념, 힌트 2 도구, 힌트 3 명령어, 힌트 4 예시) hands out the answer in instalments. Put the clues where a person in that room would find them instead:

- the manual on the desk, or a note the previous owner left
- the error code on the boot screen and the model name on the back of the case
- the in-game browser's search, and beginner tips on the in-game forum

The player then reads the error, looks around, searches, works out a fix and types it, which is the skill being taught.

- **Help must still exist for a player who is stuck.** Make it something the character consults (the manual, `man`, a forum post), reachable at any time. Removing it leaves the player with no way forward.
- **Do not announce that hints have run out** or that the player has read them all.

## Humour and Absurd Tone

For games whose voice is absurd humour (병맛). The joke lives in the fiction; the rules stay readable.

- **Names and reactions are funny; mechanics are plain.** A support system may be called '방송국', but its description states what it does and the numbers ("이 방에 있는 아군의 수리 속도가 20초 동안 50% 오릅니다"), following [write.md](write.md).
- **State the absurd situation flatly.** The humour comes from the fact itself, told with a straight face. A narrator who announces that something is ridiculous ("황당하게도", "ㅋㅋ") kills it.
- **One joke per line.** A meme or parody lands in a single line of dialogue or a title; a whole scene carried as parody wears out.
- **Choose gag or serious per scene, not per line.** A serious scene drops the memes and uses short sentences. Mixing both inside one scene makes the serious lines look like jokes.
- **Keep lines short.** One or two sentences, three at most, per text box.
- **Fix each character's speech habits.** A character's sentence endings and whether they speak formally or casually stay the same in every scene and every writer's hands.
- **Once the world coins a word, use it everywhere in the fiction.** If the setting has its own euphemism for death or defeat, crew-loss logs and event text use it too; the settings screen and real error messages outside the fiction do not.
- **Theme vocabulary still stays off the chrome.** Login, settings and store copy are plain (see "No Marketing Voice" in [write.md](write.md)).

## Before You Finish

| Pattern to search for | What to do |
| --- | --- |
| The genre name ("RPG", "시뮬레이션") shown inside the game | Delete; the store page carries it |
| An opening line that sets a mood but not who the player is or what happens today | Rewrite as the situation: role, what happened, what the player waits for |
| A menu or system with no reason in the protagonist's job | Give it one, or cut it |
| A case whose premise would be a scam or breach in the game's own world | Change the case |
| Sign-up wording (핸들, 사용할 수 있는, 실명이 아니어도) on a character form | Ask for the fact in the character's world |
| Appearance or customisation that nothing in the game shows | Give it an on-screen reason, or cut it |
| 운영자, 실행 환경, 서버, 연습용, 튜토리얼 in in-world text | Move real failures to an out-of-game notice; keep them out of the fiction |
| A system message that expresses feelings, or a character who never reacts | Machine text dry; emotion in the character's monologue |
| 힌트 1, 힌트 2, ... ending in the literal answer | Clues in the world, plus help the character can consult |
| A preview, skip or debug control in a release build | Remove behind a developer flag |
| A joke system name whose description is also a joke | Keep the name; describe the effect plainly |

## Reporting

Use the severity scale and format in [write.md](write.md#reporting). Out-of-world infrastructure text inside the fiction, a real outage presented as a story event, and a hint ladder that prints the answer are `HIGH`: they break the game the text is meant to serve. A premise that does not hold up, mood lines, sign-up wording and unused customisation are `MEDIUM`.
